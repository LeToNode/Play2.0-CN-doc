# Body parsers

## What is a body parser?

An HTTP PUT or POST request contains a body. This body can use any format, specified in the `Content-Type` request header. In Play, a **body parser** transforms this request body into a Java value. 

> **Note:** You can't write a `BodyParser` implementation directly using Java. Because a Play `BodyParser` must handle the body content incrementally using an `Iteratee[Array[Byte], A]`, it must be implemented in Scala.
>
> However Play provides default `BodyParser` implementations that should fit most use cases (parsing JSON, XML, text, uploading files). You can reuse these default parsers to create your own directly in Java; for example you could provide an RDF parser based on the text parser.

## The `BodyParser` Java API

In the Java API, all body parsers must generate a `play.mvc.Http.RequestBody` value. This value computed by the body parser can then be retrieved via `request().body()`:

```
public static Result index() {
  RequestBody body = request().body();
  ok("Got body: " + body);
}
```

You can specify the `BodyParser` to use for a particular action using the `@BodyParser.Of` annotation:

```
@BodyParser.Of(BodyParser.Json.class)
public static Result index() {
  RequestBody body = request().body();
  ok("Got json: " + body.asJson());
}
```

## The `Http.RequestBody` API

As we said above, all body parsers in the Java API will give you a `play.mvc.Http.RequestBody` value. From this body object you can retrieve the request body content in the most appropriate Java type.

> **Note:** The `RequestBody` methods, such as `asText()` or `asJson()`, will return `null` if the parser used to compute this request body doesn't support this content type. For example in an action method annotated with `@BodyParser.Of(BodyParser.Json.class)`, calling `asXml()` on the generated body will return `null`.

Some parsers can provide a most specific type than `Http.RequestBody` (i.e. a subclass of `Http.RequestBody`). You can automatically cast the request body into another type using the `as(...)` helper method:

```
@BodyParser.Of(BodyLengthParser.class)
public static Result index() {
  BodyLength body = request().body().as(BodyLength.class);
  ok("Request body length: " + body.getLength());
}
```

## Default body parser: AnyContent

If you don't specify your own body parser, Play will use the default, guessing the most appropriate content type from the `Content-Type` header:

- **text/plain**: `String`, accessible via `asText()`
- **application/json**: `JsonNode`, accessible via `asJson()`
- **text/xml**: `org.w3c.Document`, accessible via `asXml()`
- **application/form-url-encoded**: `Map<String, String[]>`, accessible via `asFormUrlEncoded()`
- **multipart/form-data**: `Http.MultipartFormData`, accessible via `asMultipartFormData()`
- Any other content type: `Http.RawBuffer`, accessible via `asRaw()`

For example:

```
public static Result save() {
  RequestBody body = request().body();
  String textBody = body.asText();
  
  if(textBody != null) {
    ok("Got: " + text);
  } else {
    badRequest("Expecting text/plain request body");
  }
}
```

## Max content length

Text based body parsers (such as **text**, **json**, **xml** or **formUrlEncoded**) have a maximum content length because they have to load all the content into memory. 

There is a default content length (the default is 100 KB). 

> **Tip:** The default content size can be defined in `application.conf`:
> 
> `parsers.text.maxLength=128K`


You can also specify a maximum content length via the `@BodyParser.Of` annotation:

```
// Accept only 10KB of data.
@BodyParser.Of(value = BodyParser.Text.class, maxLength = 10 * 1024)
public static Result index() {
  if(request().body().isMaxSizeExcedeed()) {
    return badRequest("Too much data!");
  } else {
    ok("Got body: " + request().body().asText()); 
  }
}
```

> **Next:** [[Action composition | JavaActionsComposition]]