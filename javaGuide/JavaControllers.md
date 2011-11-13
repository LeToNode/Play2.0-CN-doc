# Controllers and actions

Business logic is managed in the domain model layer. As an application client (typically a web browser) cannot directly invoke this code, the application exposes a domain object’s functionality as resources represented by URIs.

A client uses the uniform API provided by the HTTP protocol to manipulate these resources, and by implication the underlying business logic. However, this mapping of resources to domain objects is not a bijection: the granularity can be expressed at different levels. Some resources may be virtual and for some resources, aliases may be defined.

This is precisely the role played by the controller layer, which acts as glue between domain model objects and transport layer events. As with the model layer, controllers are written in pure Scala, making it easy to access or modify model objects. Like the HTTP interface, controllers are procedural and request/response oriented.

The controller layer reduces the impedance mismatch between HTTP and the domain model.

## A controller overview

A controller is a Java class, hosted by the controllers package, that subclasses `play.mvc.Controller`. 

```java
package controllers;

import play.*;
import play.mvc.*;

public class Application extends Controller {
  
  public static Result index() {
    return ok("It works!");
  }

}
```

Each public static method in a controller that returns a `play.mvc.Result` type is called an action method. The signature for an action method is always:

```java
public static play.mvc.Result action_name(params...)
```

You can define parameters in the action method signature. These parameters will be resolved by the router from the corresponding HTTP parameters.

## What’s an action?

An action is basically a Java method that processes the request parameters, and produces a result to be sent to the client.

```java
public static Result index() {
  return ok("Got request " + request() + "!");
}
```

An action returns a `play.mvc.Result` value, representing the HTTP response. In this example the `ok` method constructs a **200 OK** response containing a **text/plain** response body.

## Controllers 

A `Controller` is nothing more than a class that groups several action methods. 

The simplest syntax for defining an action is a method with not parameters that returns a `Result` value:

```java
public static Result index() {
  return ok("It works!");
}
```

Of course, the action method method can have parameters:

```java
public static Result index(String name) {
  return ok("Hello" + name);
}
```

## Composing actions

Action methods can also be easily composed using the `@With` annotation: 

```java
@With(VerboseAction.class)
public static Result index() {
  return ok("It works!");
}
```

Here is the definition of the `VerboseAction`:

```java
public static class VerboseAction extends Action.Simple {

  public Result call(Http.Context ctx) {
    Logger.info("Calling action for " + ctx);
    return delegate.call(ctx);
  }

}
```

It is also possible to define your own annotations, themselves annotated with `@With`:

```java
@With(VerboseAction.class)
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Verbose {
}
```

You can then use your new annotation with an action method:

```java
@Verbose
public static Result index() {
  return ok("It works!");
}
```




