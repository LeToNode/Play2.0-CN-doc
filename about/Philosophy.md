# Introducing Play 2.0

Since 2007, we have been working on making Java web application development easier. Play started as an internal project at [[Zenexity|http://www.zenexity.com]] and was heavily influenced by our way of doing web projects: focusing on developer productivity, respecting Web architecture, and using from the start a fresh approach to packaging conventions - breaking so-called JEE best practices where it made sense.

In 2009, we decided to share these ideas with the community as an open source project. The immediate feedback was extremely positive and the project gained a lot of traction. Today - after two years of active development - Play has several versions, an active community of 4,000 people, with a growing number of applications running in production all over the globe.

Opening a project to the world certainly means more feedback, but it also means discovering and learning about new use cases, requiring features and un-earthing bugs that we were not specifically considered in the original design and assumptions. During the two years of work on Play as an open source project we have worked to fix all of these kind of issues, as well as to integrate new features to support a wider range of scenarios. As the project has grown, we have learned a lot from our community and from our own experience - using Play in more and more complex and varied projects.

In the meantime, technology and the Web have continued to evolve. The Web has become the central point of all applications. HTML, CSS and Javascript technologies have evolved quickly - making it almost impossible for a server-side framework to follow. The whole Web architecture is fast moving towards real-time, and the emerging requirements of today's project profiles mean SQL no longer works as the exclusive datastore technology. At the programming language level we've witnessed some monumental changes with several JVM languages, including Scala, gaining popularity.

That's why we created Play 2.0, a new Web framework for a new era.

## Built for asynchronism

Today's web applications are integrating more concurrent realtime data, so web frameworks need to support a full asynchronous HTTP programming model. Play was initially designed to handle classic Web applications with many short lived requests. But now, the event model is the way to go for persistent connections - though comet, long-polling and WebSockets.

Play 2.0 is architected from the start under the assumption that every request is potentially long living. But that’s not all: we also need a powerful way to schedule and run long running tasks. The Actor based model is unquestionably the best model today to handle highly concurrent systems, and the best implementation of that model available for both Java and Scala is Akka - so it's going in. Play 2.0 provides native Akka support for Play applications, making it possible to write highly distributed systems.

## Focused on type safety

One benefit of using as statically typed programming language for writing Play applications is that the compiler can check certain parts of your code. This is not only useful for detecting mistakes early in the development process, but it also makes it a lot easier to work on large projects with a lot developers involved.

Adding Scala to the mix for Play 2.0, we clearly benefit from even stronger compiler guarantees - but that’s not enough. In Play 1.x, the template system was dynamic, based on the Groovy language, and the compiler couldn’t do much for you. As a result, errors in templates were only be detected at runtime. The same goes for verification of glue code with controllers.

We really wanted to push further this idea of having Play check most of your code at compilation time in the 2.0 version. Accordingly, we have decided to use the Scala-based template engine as the default for Play applications - even for developers using Java as the main programming language. This doesn't mean that you have to become a Scala expert to write templates in Play 2.0, just like you were not really required to know Groovy to write templates in Play 1.x. Scala is mainly used to navigate your object graph in order to display relevant information with a syntax that is very close to the Java one. However if you want to unleash the power of Scala to write advanced templates abstractions, you will quickly discover how Scala, being expression-oriented and functional, is a perfect fit for a template engine.

And that's not only true for the template engine: the routing system is also fully type-checked. Play 2.0 checks all your routes' descriptions, and verify that the everything is coherent, including the reverse routing part.

As a nice side effect, being fully compiled, the templates and route files will be easier to package and reuse and we got high performance gain on these parts at runtime.

## Native support for Java and Scala

Early in the Play project history, we started exploring the possibility of using the Scala programming language for writing Play applications. We initially introduced this work as an external module to be able to experiment freely without impacting the framework itself.

Properly integrating Scala into a Java-based framework is not trivial. Considering Scala’s compatibility with Java, one can quickly achieve a first naive integration that simply uses Scala’s syntax instead of Java’s. This, however, is certainly not the optimal way of using the language. Scala is a mix of true object orientation with functional programming. Leveraging the full power of Scala requires rethinking most of the framework’s APIs.

We quickly reached the limits of what we can do with Scala support as a separate module. Initial design choices we made in Play 1.x, relying heavily on Java reflection API and byte code manipulation, have made it harder to progress without completely rethinking some essential parts of Play’s internals. Meanwhile, we have created several awesome components for the Scala module, like the new type-safe template engine and the brand new SQL access component Anorm. So we have decided that, to fully unleash the power of Scala with Play, we moved Scala support from a separate module to the core of Play 2.0, which is designed from the beginning to natively support Scala as a programming language.

Java, on the other hand, is certainly not getting any less support from Play 2.0; quite the contrary. The Play 2.0 build provides us with an opportunity to enhance the development experience for Java developers. Java developers get a real Java API written with all the Java specificity in mind.

## Powerful build system

From the beginning of Play we have chosen a fresh way to run, compile and deploy Play applications. At the time it may have looked like an esoteric design - but it was crucial to providing an asynchronous HTTP API instead of the standard Servlet API, short feedback cycles through live compilation and reloading of source code during development, and promoting a fresh packaging approach. Consequently, It was difficult to make Play follow the standard JEE conventions.

Today, this idea of container-less deployment is more and more accepted in the Java world. It's a design choice that has allowed the Play framework to run natively on platforms like Heroku, which introduced a model that we consider the future of Java applications deployment on elastic PaaS platforms.

Existing Java build systems, however, were not flexible enough to support this new approach. Since we wanted to provide straightforward tools to run and deploy Play applications, in Play 1.x we created a collection of Python scripts to handle all build and deployment related tasks.

But developers using Play for more enterprise-scale projects, which require build process customization and integration with their existing company build systems, were a bit lost. The Python scripts we provide with Play 1.x are in no way a fully featured build system and are not easily customizable. That's why we've decided to go for a more powerful build system for Play 2.0.

As we need a modern build tool, flexible enough to support Play original conventions and able to build Java and Scala projects, we have chosen to integrate sbt in Play 2.0. This, however, should not scare existing Play users who are happy with the simplicity of Play build. We are leveraging the same simple play new, run, start experience on top of an extensible model: Play 2.0 comes with a preconfigured build script that will just work for most users. On the other hand, if you need to change the way your application is built, deployed, etc., a Play project being a standard sbt project will hand you all the power to customize and adapt it to your special needs.

This also mean better integration with Maven projects out of the box, the ability to package and publish your project as a simple set of jar files to any repository, and especially live compiling and reloading at development time of any depended project even for standard Java or Scala libraries projects.

## Datastore and Model integration

Datastore is no longer synonymous with "SQL database", and it probably never was. A lot of interesting models of datastores are becoming popular, providing different properties for different scenarios. For this reason it has become difficult for a Web framework like Play to make bold assumptions regarding the kind of datastore that will be used by developers. A generic model concept in Play no longer makes sense, since it is almost impossible to abstract over all these kinds of technology with a single API.

We wanted to make it really easy in Play 2.0 to use any datastore driver, ORM, or any another database access library without any special integration with the Web framework. We simply want to offer a minimal set of helpers to handle common technical issues, like managing the connection bounds. We also want, however, to maintain the full-stack aspect of Play framework by bundling default tools to access classical databases for users that don't have specialized needs, and that's why Play 2.0 comes with built-in relational database access library such as Ebean, JPA and Anorm.

> **Next:** [[Learn more about the Play architecture|Architecture]].
