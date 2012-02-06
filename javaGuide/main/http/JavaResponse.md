# Manipulating the response

## Changing the default Content-Type

The result Content-Type is automatically inferred from the Java value you specify as body.

For example:

```
Result textResult = ok("Hello World!");
```

Will automatically set the `Content-Type` header to `text/plain`, while:

```
Result jsonResult = ok(jerksonObject);
```

will set the `Content-Type` header to `application/json`.

This is pretty useful, but sometimes you want to change it. Just use the `as(newContentType)` method on a result to create a new similiar result with a different `Content-Type` header:

```
Result htmlResult = ok("<h1>Hello World!</h1>").as("text/html");
```

You can also for the content type on the HTTP response such as:

```
public static Result index() {
  response().setContentType("text/html");
  return ok("<h1>Hello World!</h1>");
}
```

## Setting HTTP headers

You can also add (or update) any HTTP header to the HTTP response:

```
public static Result index() {
  response().setContentType("text/html");
  response().setHeader(CACHE_CONTROL, "max-age=3600");
  response().setHeader(ETAG, "xxx");
  return ok("<h1>Hello World!</h1>");
}
```

Note that setting an HTTP header will automatically discard the previous value if it was existing.

## Setting and discarding cookies

Cookies are just a special form of HTTP headers but we provide a set of helpers to make it easier.

You can easily add a Cookie to the HTTP response using:

```
response().setCookie("theme", "blue");
```

Also, to discard a Cookie previously stored on the Web browser:

```
response().discardCookies("theme");
```

## Changing the charset for text based HTTP result.

For text based HTTP response it is very important to handle the charset correctly. Play handles that for you and uses `utf-8` by default.

The charset is used to both convert the text response to the corresponding bytes to send over the network socket, and to alterate the Content-Type header with the proper `;charset=xxx` extension.

The charset can be specified when you are generating the `Result` value:

```
public static Result index() {
  response().setContentType("text/html; charset=iso-8859-1");
  return ok("<h1>Hello World!</h1>", "iso-8859-1");
}
```

> **Next:** [[Session and Flash scopes | JavaSessionFlash]]