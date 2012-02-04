# Streaming HTTP responses

## Standard responses and Content-Length header

Since HTTP 1.1 to be able to keep a single connection open to serve several HTTP requests and responses, the server must send the appropriate `Content-Length` HTTP header along the response. 

By default when you are sending back a simple result as:

```
def index = Action {
  Ok("Hello World")
}
```

You are not specifying any Content-Length header. Of course because the content you are sending is well known, Play is able to compute the content size for you and to generate the appropriate header.

> **Note** that for text based contents it is not as simple as it looks like since the Content-Length header must be computed according the charset used to translate characters to bytes.

Actually we have seen previously that the response body is specified using an `Enumerator`:

```
def index = Action {
  SimpleResult(
    header= ResponseHeader(200),
    body = Enumerator("Hello World")
  )
}
```

So to be able to compute the `Content-Length` header properly, Play must consume the whole enumerator and load its content into memory. 

## Sending large data

If it's not a problem to load the whole content into memory for simple Enumerators what about large data set? Let's say we want to send back a large file to the Web client.

Let's first see how to create an `Enumerator[Array[Byte]]` enumerating the file content:

```
val file = new java.io.File("/tmp/fileToServe.pdf")
val fileContent: Enumerator[Array[Byte]] = Enumerator.enumerateFile(file)
```

Now it looks simple right? Let's just use this enumerator to specify the response body:

```
def index = Action {

  val file = new java.io.File("/tmp/fileToServe.pdf")
  val fileContent: Enumerator[Array[Byte]] = Enumerator.enumerateFile(file)    
    
  SimpleResult(
    header= ResponseHeader(200),
    body = fileContent
  )
}
```

Actually we have a problem here. As we don't specify the `Content-Length` header, Play will have to compute it itself, and there is no other way that trying to consume the whole enumerator content and load it into memory to compute the response size.

That's a problem for large files that we don't want to load completely into memory. So to avoid that, we just have to specify the `Content-Length` header ourself. 

```
def index = Action {

  val file = new java.io.File("/tmp/fileToServe.pdf")
  val fileContent: Enumerator[Array[Byte]] = Enumerator.enumerateFile(file)    
    
  SimpleResult(
    header= ResponseHeader(200, Map(CONTENT_LENGTH -> file.length.toString)),
    body = fileContent
  )
}
```

This way Play will just consume the body enumerator in a lazy way, copying each chunk of data to the HTTP response as soon as they are available.

## Serving files

Of course we provide easy to use helpers to this common task of serving a local file:

```
def index = Action {
  Ok.sendFile(new java.io.File("/tmp/fileToServe.pdf"))
}
```

Additionally this helper will also compute the `Content-Type` header from the file name. And it will also add the `Content-Disposition` header to specify how the Web browser should handle this response. The default is to ask the Web browser to download this file by using `Content-Disposition: attachment; filename=fileToServe.pdf`.

You also provide your own file name:

```
def index = Action {
  Ok.sendFile(
    content = new java.io.File("/tmp/fileToServe.pdf"),
    fileName = _ => "termsOfService.pdf"
  )
}
```

If you want to serve this file `inline`:

```
def index = Action {
  Ok.sendFile(
    content = new java.io.File("/tmp/fileToServe.pdf"),
    inline = true
  )
}
```

Now you don't have to specify a file name since the Web browser will not try to download it, but will just display the file content in the Web browser window (it is useful for content types supported natively by a Web browser like Html, images, etc.).

## Chunked responses

For now it works well with streaming file content since we are able to compute the content lenght before streaming it. But what about content dynamically computed with no content size available?

For this kind of response we have to use **Chunked transfer encoding**. 

> **Chunked transfer encoding** is a data transfer mechanism in version 1.1 of the Hypertext Transfer Protocol (HTTP) in which a web server serves content in a series of chunks. It uses the Transfer-Encoding HTTP response header in place of the Content-Length header, which the protocol would otherwise require. Because the Content-Length header is not used, the server does not need to know the length of the content before it starts transmitting a response to the client (usually a web browser). Web servers can begin transmitting responses with dynamically-generated content before knowing the total size of that content.
> 
> The size of each chunk is sent right before the chunk itself so that a client can tell when it has finished receiving data for that chunk. The data transfer is terminated by a final chunk of length zero.
>
> [[http://en.wikipedia.org/wiki/Chunked_transfer_encoding]]

The advantage is that we can serve the data **live** meaning that we send chunks of data as soon as they are available. The drawback is that since the Web browser doesn't know the content size, it is not able to display a proper download progress bar.

Let's say that we have a service somewhere a dynamic `InputStream` computing some data. First we have to create an `Enumerator` for this stream:

```
val data = getDataStream
val dataContent: Enumerator[Array[Byte]] = Enumerator.enumerateStream(data)
```

Now we can stream these data using a `ChunkedResult`:

```
def index = Action {

  val data = getDataStream
  val dataContent: Enumerator[Array[Byte]] = Enumerator.enumerateStream(data)
  
  ChunkedResult(
    header= ResponseHeader(200),
    chunks = dataContent
  )
}
```

Of course, as always there are helper availables to do that:

```
def index = Action {

  val data = getDataStream
  val dataContent: Enumerator[Array[Byte]] = Enumerator.enumerateStream(data)
  
  Ok.stream(dataContent)
}
```

Of course we can use any `Enumerator` to specify the chunked data:

```
def index = Action {
  Ok.stream(
    Enumerator("kiki", "foo", "bar").andThen(Enumerator.eof)
  )
}
```

> **Tip:** `Enumerator.callbackEnumerator` and `Enumerator.pushEnumerator` are two convenient way of creating reactive non-blocking enumerators in an imperative style.

We can inspect the HTTP response sent by the server:

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Transfer-Encoding: chunked

4
kiki
3
foo
3
bar
0

```

We get 3 chunks and one final empty chunk that close the response.

> **Next:** [[Comet sockets | ScalaComet]]