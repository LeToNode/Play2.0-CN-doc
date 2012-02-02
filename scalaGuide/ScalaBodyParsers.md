# Body parsers

## What is a body parser?

An HTTP request (at least for those using the POST and PUT operations) contains a body. This body can be formatted with any format specified in the Content-Type header. A **body parser** transforms this request body into a Scala value. 

However the request body for an HTTP request can be very large and a **body parser** can't just wait and load the whole data set into memory before parsing it. A `BodyParser[A]` is basically an `Iteratee[Array[Byte],A]`, meaning that it receives chunks of bytes (as long as the Web browser upload some data) and computes a value of type `A` as result.

Let's take some examples:

- A **text** body parser could accumulate chunks of bytes into a String, and give the computed String as result (`Iteratee[Array[Byte],String]`).
- A **file** body parser could store each chunk of bytes into a local file, and give a reference to the `java.io.File` as result (`Iteratee[Array[Byte],File]`).
- A **s3** body parser could push each chunk of bytes to Amazon S3 and give a the S3 object id as result (`Iteratee[Array[Byte],S3ObjectId]`).

Additionally a **body parser** has access to the HTTP request headers before starting parsing the request body and has the opportunity to run some precondition checks. It can for example check that some HTTP headers are properly set, or that the user trying to upload a large file has the permission to do it. 

> **Note**: That's why a body parser is not really an `Iteratee[Array[Byte],A]` but more precisely a `Iteratee[Array[Byte],Either[Result,A]]`, meaning that it has the opportunity to send directly an HTTP result itself (typically `400 BAD_REQUEST`, `412 PRECONDITION_FAILED` or `413 REQUEST_ENTITY_TOO_LARGE`) if it decides than it is not able to compute a correct value for the request body

Once the body parser finishes it job and give back a value of type `A`, the corresponding `Action` function is executed and the computed body value is passed into the request.

## Reminder about Actions

Previously we said that an `Action` was a `Request => Result` function. This is not totally true. Let's have a more precise look at the `Action` trait:

```
trait Action[A] extends (Request[A] ⇒ Result) {
  def parser: BodyParser[A]
}
```

First we see that there is a generic type `A`, and then that an action must define a `BodyParser[A]`. With `Request[A]` being defined as:

```
trait Request[+A] extends RequestHeader {
  def body: A
}
```

The `A` type is the type of the request body. We can use any Scala type as request body, for example `String`, `NodeSeq`, `Array[Byte]`, `JsonValue`, `java.io.File`, etc. as long as we have a body parser able to compute it.

To summarize an `Action[A]` uses a `BodyParser[A]` to retrieve a value of type `A` from the HTTP request, and to build a `Request[A]` object that is passed to the action code. 

## Default body parser: AnyContent

In our previous examples we never specified any body parser. So how can it work? If you don't specify your own body parser, Play will use the default one that compute the body as an instance of `play.api.mvc.AnyContent`.

This one check the Content-Type header and decide what kind of body to compute:

- **text/plain**: `String`
- **application/json**: `JsValue`
- **text/xml**: `NodeSeq`
- **application/form-url-encoded**: `Map[String, Seq[String]]`
- **multipart/form-data**: `MultipartFormData[TemporaryFile]`
- Any other content type: `RawBuffer`

Example:

```
def save = Action { request =>
  val body: AnyContent = request.body
  val textBody: Option[String] = body.asText 
  
  // Expecting text body
  textBody.map { text =>
    Ok("Got: " + text)
  }.getOrElse {
    BadRequest("Expecting text/plain request body")  
  }
}
```

## Specifying a body parser

The body parsers available in Play are defined in `play.api.mvc.BodyParsers.parse`.

So for example, to define an action expecting a text body (like in the previous example):

```
def save = Action(parse.text) { request =>
  Ok("Got: " + text)
}
```

Do you see how the code is simpler? It is because the `parse.text` body parser already sent a `400 BAD_REQUEST` response if something went wrong. So we don't have to check again in our action code, and we can safely assume that `request.body` contains the valid `String` body.

Alternatively we can use:

```
def save = Action(parse.tolerantText) { request =>
  Ok("Got: " + text)
}
```

This one doesn't check any Content-Type header and always load the request body as String.

> **Tip:** There is a `tolerant` fashion provided for all body parsers included in Play.

Here is another example that will store the request body into a file:

```
def save = Action(parse.file(to = new File("/tmp/upload"))) { request =>
  Ok("Saved the request content to " + request.body)
}
```

## Combining body parsers

In the previous example all request bodies are stored into the same file. This is a bit problematic isn't it? Let's write another custom body parser that extract the username from the request Session to give a unique file per user:

```
val storeInUserFile = parse.using { request =>
  request.session.get("username").map { user =>
    file(to = new File("/tmp/" + user + ".upload"))
  }.getOrElse {
    error(Unauthorized("You don't have the right to upload here"))
  }
}

def save = Action(storeInUserFile) { request =>
  Ok("Saved the request content to " + request.body)  
}

```

> **Note:** Here we are not really writing our own BodyParser, but just combining existing ones. This is often enough and should cover most use cases. Writing BodyParser from scratch is covered in the advanced topics section.

## Max content length

Text based body parsers (such as **text**, **json**, **xml** or **formUrlEncoded**) use a max content length because they have to load all the content into memory. 

There is a default content length (the default is 100KB), but you can also specify it online:

```
// Accept only 10KB of data.
def save = Action(parse.text(maxLength = 1024 * 10)) { request =>
  Ok("Got: " + text)
}
```

> **Tip:** The default content size can be defined in `application.conf`:
> 
> `parsers.text.maxLength=128K`

You can also wrap any body parser with `maxLength`:

```
// Accept only 10KB of data.
def save = Action(maxLength(1024 * 10, parser = storeInUserFile)) { request =>
  Ok("Saved the request content to " + request.body)  
}
```

> **Next:** [[Actions composition | ScalaActionsComposition]]