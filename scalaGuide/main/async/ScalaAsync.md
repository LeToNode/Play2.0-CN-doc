# Handling asynchronous results

## Why asynchonous results?

Until now we were able to compute the result to send to the Web client directly. But it is not always the case: the result can depend of an expensive computation or of a long Web service call.

Because of the way Play 2.0 works, the action code must be as fast as possible (ie. non blocking). So what should we return as result if we are not yet able to compute it? The response is a promise of result! 

A `Promise[Result]` will eventually be redeemed with a value of type `Result`. By giving a `Promise[Result]` instead if a classical `Result`, we are able to compute the result quickly without blocking anything. And Play will serve this result as soon as the promise is redeemed. 

The Web client will be blocked while waiting for the response but nothing will be blocked on the server, and the resources can be used to serve other clients.

## How to create a `Promise[Result]`

To create a `Promise[Result]` we need another promise first: the promise that will give us the actual value we need to compute the result:

```
val promiseOfPIValue: Promise[Double] = computePIAsynchronously()
val promiseOfResult: Promise[Result] = promiseOfPIValue.map { pi =>
  Ok("PI value computed: " + pi)    
}
```

All Play 2.0 asynchronous API will give you a `Promise`. It is the case when you are calling external Web service using the `play.api.libs.WS` API, or if you are using Akka to schedule asynchonous tasks or to communicate with Actors using `play.api.libs.Akka`.

A simple way to execute a block of code asynchronously and to get a `Promise` is to use the `play.api.libs.Akka` helpers:

```
val promiseOfInt: Promise[Int] = Akka.future {
  intensiveComputation()
}
```

> **Note:** Here the intensive computation will just be run on another thread. It is also possible to run it remotly on a cluster of backend servers using Akka remote.

## AsyncResult

While we were using `SimpleResult` until now, to send an asynchronous result we need an `AsyncResult` wrapping the actual `SimpleResult`:

```
def index = Action {
  val promiseOfInt = Akka.future { intensiveComputation() }
  Async {
    promiseOfInt.map(i => Ok("Got result: " + i))
  }
}
```

> **Note:** `Async { }` is an helper method building an `AsyncResult` from a `Promise[Result]`.

## Handling timeout

It is often useful to handle timeout properly to avoid to let the Web browser blocked waiting if something goes wrong. You can easily compose promise with promise timeout to handle these cases:

```
def index = Action {
  val promiseOfInt = Akka.future { intensiveComputation() }
  Async {
    promiseOfInt.orTimeout("Oops", 1000).map { eitherIntorTimeout =>
      eitherIorTimeout.fold(
        timeout => InternalServerError(timeout),
        i => Ok("Got result: " + i)    
      )    
    }  
  }
}
```

> **Next:** [[Streaming HTTP responses | ScalaStream]]