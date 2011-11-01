# Controllers and Actions

Business logic is managed in the domain model layer. As a client (typically a web browser) cannot directly invoke this code, the functionality of a domain object is exposed as resources represented by URIs.

A client uses the uniform API provided by the HTTP protocol to manipulate these resources, and by implication the underlying business logic. However, this mapping of resources to domain objects is not a bijection: the granularity can be expressed at different levels, some resources may be virtual, for some resources aliases may be defined…

This is precisely the role played by the Controller layer: providing a glue between the domain model objects and transport layer events. As the Model layer, controllers are written in pure Java, making it easy to access or modify Model objects. Like the HTTP interface, Controllers are procedural and Request/Response oriented.

> The Controller layer reduces the impedance mismatch between HTTP and the Domain Model.

## A controller overview

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

