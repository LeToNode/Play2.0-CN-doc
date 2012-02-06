# Handling asynchronous results

## Why asynchonous results?

Until now we were able to compute the result to send to the Web client directly. But it is not always the case: the result can depend of an expensive computation or of a long Web service call.

Because of the way Play 2.0 works, the action code must be as fast as possible (ie. non blocking). So what should we return as result if we are not yet able to compute it? The response is a promise of result! 

A `Promise<Result>` will eventually be redeemed with a value of type `Result`. By giving a `Promise<Result>` instead if a classical `Result`, we are able to compute the result quickly without blocking anything. And Play will serve this result as soon as the promise is redeemed. 

The Web client will be blocked while waiting for the response but nothing will be blocked on the server, and the resources can be used to serve other clients.

## How to create a `Promise<Result>`

To create a `Promise<Result>` we need another promise first: the promise that will give us the actual value we need to compute the result:

```
Promise<Double> promiseOfPIValue = computePIAsynchronously();
Promise<Result> promiseOfResult = promiseOfPIValue.map(
  new Function<Double,Result>() {
    public Result apply(Double pi) {
      return ok("PI value computed: " + pi);
    } 
  }
);
```

> **Note:** Writing functional composition in Java is really verbose for now. But it should be better when Java will support [[lambda notation| http://mail.openjdk.java.net/pipermail/lambda-dev/2011-September/003936.html]].

All Play 2.0 asynchronous API will give you a `Promise`. It is the case when you are calling external Web service using the `play.libs.WS` API, or if you are using Akka to schedule asynchonous tasks or to communicate with Actors using `play.libs.Akka`.

A simple way to execute a block of code asynchronously and to get a `Promise` is to use the `play.libs.Akka` helpers:

```
Promise<Integer> promiseOfInt = Akka.future(
  new Callable<Integer>() {
    public Integer call() {
      intensiveComputation();
    }
  }
);
```

> **Note:** Here the intensive computation will just be run on another thread. It is also possible to run it remotly on a cluster of backend servers using Akka remote.

## AsyncResult

While we were using `Results.Status` until now, to send an asynchronous result we need an `Results.AsyncResult` wrapping the actual result:

```
public static Result index() {
  Promise<Integer> promiseOfInt = Akka.future(
    new Callable<Integer>() {
      public Integer call() {
        intensiveComputation();
      }
    }
  );
  async(
    promiseOfInt.map(
      new Function<Integer,Result>() {
        public Result apply(Integer i) {
          return ok("Got result: " + i);
        } 
      }
    )
  );
}
```

> **Note:** `async()` is an helper method building an `AsyncResult` from a `Promise<Result>`.

> **Next:** [[Streaming HTTP responses | JavaStream]]