# Controllers and Actions

Business logic is managed in the domain model layer. As a client (typically a web browser) cannot directly invoke this code, the functionality of a domain object is exposed as resources represented by URIs.

A client uses the uniform API provided by the HTTP protocol to manipulate these resources, and by implication the underlying business logic. However, this mapping of resources to domain objects is not a bijection: the granularity can be expressed at different levels, some resources may be virtual, for some resources aliases may be defined…

This is precisely the role played by the Controller layer: providing a glue between the domain model objects and transport layer events. As the Model layer, controllers are written in pure Java, making it easy to access or modify Model objects. Like the HTTP interface, Controllers are procedural and Request/Response oriented.

The Controller layer reduces the impedance mismatch between HTTP and the Domain Model.

## A Controller overview

A Controller is a Java class, hosted by the controllers package, and subclassing `play.mvc.Controller`. 

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

Each public, static method in a Controller returning a `play.mvc.Result` type is called an action method. The signature for an action method is always:

```java
public static play.mvc.Result action_name(params...)
```

You can define parameters in the action method signature. These parameters will be resolved by the Router from the corresponding HTTP parameters.

## What's an Action?

An action is basically a Java method processing the request paramters and producing a result to be sent to the client.

```java
public static Result index() {
  return ok("Got request " + request() + "!");
}
```

An action returns a `play.mvc.Result` value, representing the HTTP response to send to the web client. In this example the `ok` methid constructs a **200 OK** response containing a **text/plain** response body.

## Controllers 

Actually a `Controller` is nothing more than a class regrouping several action methods. 

The simplest use case to define an action is a simple method without parameters returning an `Result` value.:

```java
public static Result index() {
  return ok("It works!");
}
```

Of course the action method method can have parameters:

```java
public static Result index(String name) {
  return ok("Hello" + name);
}
```

## Composing Actions

Action can easily be composed using the `@With` annotation: 

```java
@With(VerboseAction.class)
public static Result index() {
  return ok("It works!");
}
```

And here is the definition of the `VerboseAction`:

```java
public static class VerboseAction extends Action.Simple {

  public Result call(Http.Context ctx) {
    Logger.info("Calling action for " + ctx);
    return delegate.call(ctx);
  }

}
```

It is also possible to define your own annotations, annotated themself using `@With`:

```java
@With(VerboseAction.class)
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Verbose {
}
```

And then:

```java
@Verbose
public static Result index() {
  return ok("It works!");
}
```




