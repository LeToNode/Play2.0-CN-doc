# Streaming HTTP responses

## Standard responses and Content-Length header

Since HTTP 1.1 to be able to keep a single connection open to serve several HTTP requests and responses, the server must send the appropriate `Content-Length` HTTP header along the response. 

By default when you are sending back a simple result as:

```
public static Result index() {
  return ok("Hello World")
}
```

You are not specifying any Content-Length header. Of course because the content you are sending is well known, Play is able to compute the content size for you and to generate the appropriate header.

> **Note** that for text based contents it is not as simple as it looks like since the Content-Length header must be computed according the charset used to translate characters to bytes.

To be able to compute the `Content-Length` header properly, Play must consume the whole response data and load its content into memory. 

## Serving files

If it's not a problem to load the whole content into memory for simple content what about large data set? Let's say we want to send back a large file to the Web client.

Of course we provide easy to use helpers to this common task of serving a local file:

```
public static Result index() {
  return ok(new java.io.File("/tmp/fileToServe.pdf"));
}
```

Additionally this helper will also compute the `Content-Type` header from the file name. And it will also add the `Content-Disposition` header to specify how the Web browser should handle this response. The default is to ask the Web browser to download this file by using `Content-Disposition: attachment; filename=fileToServe.pdf`.

## Chunked responses

For now it works well with streaming file content since we are able to compute the content length before streaming it. But what about content dynamically computed with no content size available?

For this kind of response we have to use **Chunked transfer encoding**. 

> **Chunked transfer encoding** is a data transfer mechanism in version 1.1 of the Hypertext Transfer Protocol (HTTP) in which a web server serves content in a series of chunks. It uses the Transfer-Encoding HTTP response header in place of the Content-Length header, which the protocol would otherwise require. Because the Content-Length header is not used, the server does not need to know the length of the content before it starts transmitting a response to the client (usually a web browser). Web servers can begin transmitting responses with dynamically-generated content before knowing the total size of that content.
> 
> The size of each chunk is sent right before the chunk itself so that a client can tell when it has finished receiving data for that chunk. The data transfer is terminated by a final chunk of length zero.
>
> [[http://en.wikipedia.org/wiki/Chunked_transfer_encoding]]

The advantage is that we can serve the data **live** meaning that we send chunks of data as soon as they are available. The drawback is that since the Web browser doesn't know the content size, it is not able to display a proper download progress bar.

Let's say that we have a service somewhere a dynamic `InputStream` computing some data. We can ask Play to stream this content directly using a chunked response:

```
public static Result index() {
  InputStream is = getDynamicStreamSomewhere();
  return ok(is);
}
```

You can also set up your own chunked response builder. The Play Java API support both text and binary chunked streams (via `String` and `byte[]`):

```
public static index() {
  // Prepare a chunked text stream
  Chunks<String> chunks = new StringChunks() {
    
    // Called when the stream is ready
    public void onReady(Chunks.Out<String> out) {
      registerOutChannelSomewhere(out);
    }
    
  }
  
  // Serves this stream with 200 OK
  ok(chunks);
}
```

The `onReady` method is called when it is safe to write on this stream. It gives you a `Chunks.Out` channel you can write one.

Let's say we have an asynchronous process (like an `Actor`) somewhere pushing to this stream:

```
public void registerOutChannelSomewhere(Chunks.Out<String> out) {
  out.write("kiki");
  out.write("foo");
  out.write("bar");
  out.close();
}
```

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

> **Next:** [[Comet sockets | JavaComet]]