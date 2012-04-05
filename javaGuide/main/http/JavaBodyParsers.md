# 内容处理

## 什么是内容处理?

一个HTTP请求(至少是使用了POST和PUT操作的)的内容可以被格式化成任何指定的格式，指定格式在Content-Type header。一个**内容解析器**负责将请求的内容转成对应的java值

> **注意:** 你不能直接用java来重写'内容解析器'的实现，因为一个play'内容解析器'必须使用'Iteratee[Array[Byte], A]'来处理内容，'Iteratee[Array[Byte], A]'必须用scala来实现。

>
> 但是play提供绝大多数的默认的'内容解析器'(parsing Json, Xml, Text, uploading files),你可以重用默认的解析器来创建你自己的java实现的解析器；比如你可以在Text解析器的基础上自定义一个PDF解析器

## `内容解析器` 的Java API

In the Java API, all body parsers must generate a `play.mvc.Http.RequestBody` value. This value computed by the body parser can then be retrieved via `request().body()`:

```
public static Result index() {
  RequestBody body = request().body();
  return ok("Got body: " + body);
}
```

You can specify the `BodyParser` to use for a particular action using the `@BodyParser.Of` annotation:

```
@BodyParser.Of(BodyParser.Json.class)
public static Result index() {
  RequestBody body = request().body();
  return ok("Got json: " + body.asJson());
}
```

## The `Http.RequestBody` API

As we just said all body parsers in the Java API will give you a `play.mvc.Http.RequestBody` value. From this body object you can retrieve the request body content in the most appropriate Java type.

> **Note:** The `RequestBody` methods like `asText()` or `asJson()` will return null if the parser used to compute this request body doesn't support this content type. For example in an action method annotated with `@BodyParser.Of(BodyParser.Json.class)`, calling `asXml()` on the generated body will retun null.

Some parsers can provide a most specific type than `Http.RequestBody` (ie. a subclass of `Http.RequestBody`). You can automatically cast the request body into another type using the `as(...)` helper method:

```
@BodyParser.Of(BodyLengthParser.class)
pulic static Result index() {
  BodyLength body = request().body().as(BodyLength.class);
  ok("Request body length: " + body.getLength());
}
```

## 默认内容处理: 任意内容

If you don't specify your own body parser, Play will use the default one guessing the most appropriate content type from the `Content-Type` header:

- **text/plain**: `String`, accessible via `asText()`
- **application/json**: `JsonNode`, accessible via `asJson()`
- **text/xml**: `org.w3c.Document`, accessible via `asXml()`
- **application/form-url-encoded**: `Map<String, String[]>`, accessible via `asFormUrlEncoded()`
- **multipart/form-data**: `Http.MultipartFormData`, accessible via `asMultipartFormData()`
- Any other content type: `Http.RawBuffer`, accessible via `asRaw()`

Example:

```
pulic static Result save() {
  RequestBody body = request().body();
  String textBody = body.asText();
  
  if(textBody != null) {
    ok("Got: " + text);
  } else {
    badRequest("Expecting text/plain request body");
  }
}
```

## 最大内容长度

Text based body parsers (such as **text**, **json**, **xml** or **formUrlEncoded**) use a max content length because they have to load all the content into memory. 

There is a default content length (the default is 100KB). 

> **Tip:** The default content size can be defined in `application.conf`:
> 
> `parsers.text.maxLength=128K`


You can also specify a maximum content length via the `@BodyParser.Of` annotation:

```
// Accept only 10KB of data.
@BodyParser.Of(value = BodyParser.Text.class, maxLength = 10 * 1024)
pulic static Result index() {
  if(request().body().isMaxSizeExceeded()) {
    return badRequest("Too much data!");
  } else {
    ok("Got body: " + request().body().asText()); 
  }
}
```

> **Next:** [[Actions composition | JavaActionsComposition]]