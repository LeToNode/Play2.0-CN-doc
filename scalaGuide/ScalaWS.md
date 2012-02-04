# The Play WS API

Sometimes we would like to call other HTTP services from within a play application. Play supports this task via its `play.api.libs.ws.WS` library which provides a way to make asynchronous HTTP calls. 

Any calls made by `play.api.libs.WS` should return a `Promise[play.api.libs.ws.Response]` which we can later handle with play's asynchronous mechanisms.

## Making HTTP call

To make an HTTP request you start with `WS.url()` to specify the URL. Then you get a builder that you can use to specify several HTTP options (like setting headers). You end by calling a final method corresponding to the HTTP method you want to use:

```
val homePage: Promise[ws.Response] = WS.url("http://mysite.com").get()
```

Or:

```
val result: Promise[ws.Response] = {
  WS.url("http://localhost:9001/post").post("content")
}
```

## Retrieving the HTTP response result

The call is made asynchronously and you need to manipulate it as a `Promise[ws.Response]` to get the actual content. You can compose several promises and ends with a `Promise[Result]` that can be handled directly by the Play server:

```
def feedTitle(feedUrl: String) = Action {
  WS.url(feedUrl).get().map { response =>
    Ok("Feed title: + (response.json / "title").as[String])
  }  
}
```

> **Next:** [[Integrating with Akka | ScalaAkka]]
