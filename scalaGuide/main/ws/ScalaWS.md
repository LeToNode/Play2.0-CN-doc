# The Play WS API

Sometimes we would like to call other HTTP services from within a Play application. Play supports this via its `play.api.libs.ws.WS` library, which provides a way to make asynchronous HTTP calls.

Any calls made by `play.api.libs.ws.WS` should return a `Promise[play.api.libs.ws.Response]` which we can later handle with Play’s asynchronous mechanisms.

## Making an HTTP call

To send an HTTP request you start with `WS.url()` to specify the URL. Then you get a builder that you can use to specify various HTTP options, such as setting headers. You end by calling a final method corresponding to the HTTP method you want to use. For example:

```scala
val homePage: Promise[ws.Response] = WS.url("http://mysite.com").get()
```

Or:

```scala
val result: Promise[ws.Response] = {
  WS.url("http://localhost:9001/post").post("content")
}
```

## Retrieving the HTTP response result

The call is asynchronous and you need to manipulate it as a `Promise[ws.Response]` to get the actual content. You can compose several promises and end with a `Promise[Result]` that can be handled directly by the Play server:

```scala
def feedTitle(feedUrl: String) = Action {
  Async {
    WS.url(feedUrl).get().map { response =>
      Ok("Feed title: " + (response.json \ "title").as[String])
    }
  }  
}
```

> **Next:** [[OpenID Support in Play | ScalaOpenID]]