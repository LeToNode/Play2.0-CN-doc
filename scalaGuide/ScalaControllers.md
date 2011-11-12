# Controllers and actions

Business logic is managed in the domain model layer. As an application client (typically a web browser) cannot directly invoke this code, the application exposes a domain object’s functionality as resources represented by URIs.

A client uses the uniform API provided by the HTTP protocol to manipulate these resources, and by implication the underlying business logic. However, this mapping of resources to domain objects is not a bijection: the granularity can be expressed at different levels. Some resources may be virtual and for some resources, aliases may be defined.

This is precisely the role played by the controller layer, which acts as glue between domain model objects and transport layer events. As with the model layer, controllers are written in pure Scala, making it easy to access or modify model objects. Like the HTTP interface, controllers are procedural and request/response oriented.

The controller layer reduces the impedance mismatch between HTTP and the domain model.

## A controller overview

A Controller is a Scala singleton object, hosted by the controllers package, that subclasses `play.api.mvc.Controller`. You can declare as many controllers you want in the same file.

```scala
package controllers

import play.api._
import play.api.mvc._

object Application extends Controller {
  
  def index = Action {
    Ok("It works!")
  }

}
```

A controller defines several `Action` generators, such as the `index` method in the above example.

## What’s an action?

An `play.api.mvc.Action` is basically a `(Request => Result)` function that handles a request and generates a result to be sent to the client.

```scala
val echo = Action { request =>
  Ok("Got request [" + request + "]")
}
```

An action returns a `play.api.mvc.Result` value, representing the HTTP response to send to the web client. In this example `Ok` constructs a **200 OK** response containing a **text/plain** response body.

## Controllers as action generators

A `Controller` is nothing more than a singleton object that generates `Action` values. The simplest use case for defining an action generator is a method with no parameters that returns an `Action` value	:

```scala
def index = Action {
  Ok("It works!")
}
```

Often, your action will need a reference to the HTTP request, so you can define an `Action` accepting a `play.api.mvc.Request` value as input:

```scala
def index = Action { request =>
  val name = request.queryString.get("name").flatMap(_.headOption)
  Ok("Hello " + name.getOrElse("Guest"))
}
```

Note, that it can be useful to define the `request` parameter as **implicit**, since many API methods need a request as input.

```scala
def index = Action { implicit request =>
  Ok("It works!")
}
```

Of course, the action generator method can have parameters, and these parameters can be captured by the `Action` closure:

```scala
def hello(name: String) = Action {
  Ok("Hello " + name)
}
```

## Composing actions

You can easily compose actions. For example, you can define a cached and secured action using:

```scala
def hello(name: String) = {
  Authenticated {
    Cached {
      Action {
        Ok("Hello " + name)
      }
    }      
  }
}
```



