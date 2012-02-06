# The Play WS API

Sometimes we would like to call other HTTP services from within a play application. Play supports this task via its `play.libs.WS` library which provides a way to make asynchronous HTTP calls. 

Any calls made by `play.libs.WS` should return a `Promise<Ws.Response>` which we can later handle with play's asynchronous mechanisms.

## Making HTTP call

To make an HTTP request you start with `WS.url()` to specify the URL. Then you get a builder that you can use to specify several HTTP options (like setting headers). You end by calling a final method corresponding to the HTTP method you want to use:

```
Promise<WS.Response> homePage = WS.url("http://mysite.com").get();
```

Or:

```
Promise<WS.Response> result = WS.url("http://localhost:9001").post("content");
```

## Retrieving the HTTP response result

The call is made asynchronously and you need to manipulate it as a `Promise<WS.Response>` to get the actual content. You can compose several promises and ends with a `Promise<Result>` that can be handled directly by the Play server:

```
public static index feedTitle(String feedUrl) {
  return async(
    WS.url(feedUrl).get().map(
      new Function<WS.Response, Result>() {
        public Result apply(WS.Response response) {
          return ok("Feed title: + response.asJson().findPath("title));
        }
      }
    );
  );
}
```

> **Next:** [[Integrating with Akka | JavaAkka]]
