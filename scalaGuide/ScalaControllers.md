# Controllers and Actions

Business logic is managed in the domain model layer. As a client (typically a web browser) cannot directly invoke this code, the functionality of a domain object is exposed as resources represented by URIs.

A client uses the uniform API provided by the HTTP protocol to manipulate these resources, and by implication the underlying business logic. However, this mapping of resources to domain objects is not a bijection: the granularity can be expressed at different levels, some resources may be virtual, for some resources aliases may be defined…

This is precisely the role played by the Controller layer: providing a glue between the domain model objects and transport layer events. As the Model layer, controllers are written in pure Java, making it easy to access or modify Model objects. Like the HTTP interface, Controllers are procedural and Request/Response oriented.

The Controller layer reduces the impedance mismatch between HTTP and the Domain Model.

## A Controller overview

A Controller is a Scala singleton object, hosted by the controllers package, and subclassing `play.api.mvc.Controller`. You can declare as many controllers you want in the same file.

```scala
package controllers

import play.api.__
import play.api.mvc._

object Application extends Controller {
  
  def index = Action {
    Ok("It works!")
  }

}
```

A controller defines several `Action` generators, like the `index` method in this example.

## What's an Action?

An `play.api.mvc.Action` is basically a `(Request => Result)` function, handling a request and generating a result to be sent to the client.

```scala
val echo = Action { request =>
  Ok("Got request [" + request + "]")
}
```

An Action returns a `play.api.mvc.Result` value, representing the HTTP response to send to the web client. In this example `Ok` constructs a **200 OK** response containing a **text/plain** response body.

## Controllers as Action generators

Actually a `Controller` is nothing more than a singleton object generating `Action` values. 

The simplest use case to define an action generator is a simple method without parameters returning an `Action` value.:

```scala
def index = Action {
  Ok("It works!")
}
```

Often, your action will need a reference on the HTTP request, so you can defines a `Action` accepting a `play.api.mvc.Request` value as input:

```scala
def index = Action { request =>
  Ok("It works!")
}
```

Note, that it is useful to define the `request` parameter as **implicit**, as many API needs a request as input.

```scala
def index = Action { implicit request =>
  Ok("It works!")
}
```





