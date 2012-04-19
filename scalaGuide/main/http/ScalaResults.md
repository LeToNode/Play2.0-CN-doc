# Manipulating Results

## Changing the default Content-Type

The result content type is automatically inferred from the Scala value that you specify as the response body.

For example:

```scala
val textResult = Ok("Hello World!")
```

Will automatically set the `Content-Type` header to `text/plain`, while:

```scala
val xmlResult = Ok(<message>Hello World!</message>)
```

will set the Content-Type header to `text/xml`.

> **Tip:** this is done via the `play.api.http.ContentTypeOf` type class.

This is pretty useful, but sometimes you want to change it. Just use the `as(newContentType)` method on a result to create a new similar result with a different `Content-Type` header:

```scala
val htmlResult = Ok(<h1>Hello World!</h1>).as("text/html")
```

or even better, using:

```scala
val htmlResult = Ok(<h1>Hello World!</h1>).as(HTML)
```

> **Note:** The benefit of using `HTML` instead of the `"text/html"` is that the charset will be automatically handled for you and the actual Content-Type header will be set to `text/html; charset=utf-8`. We will see that in a bit.

## Manipulating HTTP headers

You can also add (or update) any HTTP header to the result:

```scala
Ok("Hello World!").withHeaders(
  CACHE_CONTROL -> "max-age=3600", 
  ETAG -> "xx"
)
```

Note that setting an HTTP header will automatically discard the previous value if it was existing in the original result.

## Setting and discarding cookies

Cookies are just a special form of HTTP headers but we provide a set of helpers to make it easier.

You can easily add a Cookie to the HTTP response using:

```scala
Ok("Hello world").withCookies(
  Cookie("theme", "blue")
)
```

Also, to discard a Cookie previously stored on the Web browser:

```scala
Ok("Hello world").discardingCookies("theme")
```

## Changing the charset for text based HTTP responses.

For text based HTTP response it is very important to handle the charset correctly. Play handles that for you and uses `utf-8` by default.

The charset is used to both convert the text response to the corresponding bytes to send over the network socket, and to alterate the `Content-Type` header with the proper `;charset=xxx` extension.

The charset is handled automatically via the `play.api.mvc.Codec` type class. Just import an implicit instance of `play.api.mvc.Codec` in the current scope to change the charset that will be used by all operations:

```scala
object Application extends Controller {
    
  implicit val myCustomCharset = Codec.javaSupported("iso-8859-1")
    
  def index = Action {
    Ok(<h1>Hello World!</h1>).as(HTML)
  }
    
}
```

Here, because there is an implicit charset value in the scope, it will be used by both the `Ok(...)` method to convert the XML message into `ISO-8859-1` encoded bytes and to generate the `text/html; charset=iso-8859-1` Content-Type header.

Now if you are wondering how the `HTML` method works, here it is how it is defined:

```scala
def HTML(implicit codec: Codec) = {
  "text/html; charset=" + codec.charset
}
```

You can do the same in your API if you need to handle the charset in a generic way.

> **Next:** [[Session and Flash scopes | ScalaSessionFlash]]