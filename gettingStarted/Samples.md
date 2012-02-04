# Sample applications

The Play 2.0 package comes with a comprehensive set of sample applications written in both Java and Scala. This is a very good place to look for code snippets and examples.

> The sample applications are available in the `samples/` directory of your Play installation.

## Hello world

[[images/helloworld.png]]

This is a very basic application that demonstrates Play 2.0 fundamentals:

- Writing controllers and actions.
- Routing and reverse routing.
- Linking to public assets.
- Using the template engine.
- Handling forms with validation.

## Computer database

[[images/computerdatabase.png]]

This is a classic CRUD application, backed by a JDBC database. It demonstrates:

- Accessing a JDBC database, using Ebean in Java and Anorm in Scala.
- Achieving, table pagination and CRUD forms.
- Integrating with a CSS framework ([[Twitter Bootstrap | http://twitter.github.com/bootstrap/]]).

Twitter Bootstrap requires a different form layout to the default one that the Play 2.0 form helper generates, so this application also provides an example of integrating a custom form input constructor.

## Forms

[[images/forms.png]]

This is a dummy application presenting several typical form usages. It demonstrates: 

- Writing complex forms with validation.
- Handling forms with dynamically repeated values.

## ZenTasks

[[images/zentask.png]]

This advanced todo list demonstrates a modern AJAX-based web application. This is a work in progress, and we plan to add several features in the future releases. For now you can check it out to learn how to:

- Integrate authentication, and security.
- Use AJAX and the Javascript reverse routing.
- Integrate with compiled assets - LESS CSS and CoffeeScript.

## CometClock

[[images/comet-clock.png]]

This a very simple Comet demonstration pushing clock events from the server to the Web browser using a the forever-frame technique. It demonstrates: 

- Create a Comet connection.
- Use Akka actors (in the Java version).
- Write custom Enumerators (in the Scala version).

## WebSocket chat

[[images/websocket-chat.png]]

A Chat room built using WebSockets. Additionally there is a bot used speaking on the same chat room. It demonstrates:

- WebSocket connections.
- Advanced usage of Akka.

> **Next:** 
>
>   - [[Play 2.0 for Scala developers | ScalaHome]]
>   - [[Play 2.0 for Java developers | JavaHome]]