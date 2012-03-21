# Play 2.0 介绍

从2007年开始，我们就致力于让Java开发变得更加容易. Play最开始属于[[Zenexity|http://www.zenexity.com]]的一个内部项目，当时我们进行Web开发的方式产生了巨大影响: 专注于开发效率, 注重Web架构, 使用一种新的方式在项目开始就进行一些约定 - 打破了所谓的J2EE最佳实践的那些场景。

2009年，我们决定把这些想法贡献给开源社区. 正面的反馈信息出现的非常快，这些反馈也给了我们的发展很大东里. 现在 - 也就是经过了两年的持续更新 - Play拥有了不少版本, 一个约4000人左右的活跃社区, 和不断在世界各地持续增长的基于Play的项目.

把一个项目开放给全世界意味着更多的反馈, 同时也意味着能够发现和学习更多的用户使用案例、更多我们在设计之处没有想到的功能和情景. 在Play开源两年来的时间里，我们做了很多工作去解决和改善此类问题, 同时也努力集成其他一些工具以适应更广泛的使用场景. 随着项目的发展，我们从开源社区和我们自己的开发经历(在更复杂和更多变的项目中使用Play)中学到了很多东西。

同时，技术和WEB也在不断的发展. Web已经成为了各种应用的核心. HTML, CSS 和 JavaScript 发展的很迅速 - 导致服务器端技术很难跟上节奏. 整个Web架构逐渐趋向于实时处理和相应（Real-time processing and response）, 并且新型的需求不在依靠SQL数据库作为唯一的存储技术. 从语言层面上，我们也见证了很多JVM语言的兴起，如Scala变得越来越流行.

这就是为什么我们创造了 Play 2.0, 一个新时代的崭新Web框架.

## 为异步编程而生

当今的Web应用需要越来越大的并发能力, 因此就需要Web框架对异步HTTP开发提供完整的支持. Play最初的设计是，用很多短连接(short-lived)处理用户请求. 但是现在, the event model is the way to go for persistent connections - though Comet, long-polling and WebSockets.

Play 2.0 is architected from the start under the assumption that every request is potentially long-lived. But that’s not all: we also need a powerful way to schedule and run long-running tasks. The Actor-based model is unquestionably the best model today to handle highly concurrent systems, and the best implementation of that model available for both Java and Scala is Akka - so it’s going in. Play 2.0 provides native Akka support for Play applications, making it possible to write highly-distributed systems.

## 注重类型安全

One benefit of using a statically-typed programming language for writing Play applications is that the compiler can check parts of your code. This is not only useful for detecting mistakes early in the development process, but it also makes it a lot easier to work on large projects with a lot developers involved.

Adding Scala to the mix for Play 2.0, we clearly benefit from even stronger compiler guarantees - but that’s not enough. In Play 1.x, the template system was dynamic, based on the Groovy language, and the compiler couldn’t do much for you. As a result, errors in templates could only be detected at run-time. The same goes for verification of glue code with controllers.

In version 2.0, we really wanted to push this idea of having Play check most of your code at compilation time further. This is why we decided to use the Scala-based template engine as the default for Play applications - even for developers using Java as the main programming language. This doesn’t mean that you have to become a Scala expert to write templates in Play 2.0, just as you were not really required to know Groovy to write templates in Play 1.x.

In templates, Scala is mainly used to navigate your object graph in order to display relevant information, with a syntax that is very close to Java’s. However, if you want to unleash the power of Scala to write advanced templates abstractions, you will quickly discover how Scala, being expression-oriented and functional, is a perfect fit for a template engine.

And that’s not only true for the template engine: the routing system is also fully type-checked. Play 2.0 checks your routes’ descriptions, and verifies that everything is consistent, including the reverse routing part.

A nice side effect of being fully compiled is that the templates and route files will be easier to package and reuse. You also get a significant performance gain on these parts at run-time.

## 原生支持Java和Scala

Early in the Play project’s history, we started exploring the possibility of using the Scala programming language for writing Play applications. We initially introduced this work as an external module, to be able to experiment freely without impacting the framework itself.

Properly integrating Scala into a Java-based framework is not trivial. Considering Scala’s compatibility with Java, one can quickly achieve a first naive integration that simply uses Scala’s syntax instead of Java’s. This, however, is certainly not the optimal way of using the language. Scala is a mix of true object orientation with functional programming. Leveraging the full power of Scala requires rethinking most of the framework’s APIs.

We quickly reached the limits of what we can do with Scala support as a separate module. Initial design choices we made in Play 1.x, relying heavily on Java reflection API and byte code manipulation, have made it harder to progress without completely rethinking some essential parts of Play’s internals. Meanwhile, we have created several awesome components for the Scala module, such as the new type-safe template engine and the brand new SQL access component Anorm. This is why we decided that, to fully unleash the power of Scala with Play, we would move Scala support from a separate module to the core of Play 2.0, which is designed from the beginning to natively support Scala as a programming language.

Java, on the other hand, is certainly not getting any less support from Play 2.0; quite the contrary. The Play 2.0 build provides us with an opportunity to enhance the development experience for Java developers. Java developers get a real Java API written with all the Java specificity in mind.

## 强大的构建系统

From the beginning of the Play project, we have chosen a fresh way to run, compile and deploy Play applications. It may have looked like an esoteric design at first, but it was crucial to providing an asynchronous HTTP API instead of the standard Servlet API, short feedback cycles through live compilation and reloading of source code during development, and promoting a fresh packaging approach. Consequently, it was difficult to make Play follow the standard JEE conventions.

Today, this idea of container-less deployment is increasingly accepted in the Java world. It’s a design choice that has allowed the Play framework to run natively on platforms like Heroku, which introduced a model that we consider the future of Java application deployment on elastic PaaS platforms.

Existing Java build systems, however, were not flexible enough to support this new approach. Since we wanted to provide straightforward tools to run and deploy Play applications, in Play 1.x we created a collection of Python scripts to handle build and deployment tasks.

Meanwhile, developers using Play for more enterprise-scale projects, which require build process customization and integration with their existing company build systems, were a bit lost. The Python scripts we provide with Play 1.x are in no way a fully-featured build system and are not easily customizable. That’s why we’ve decided to go for a more powerful build system for Play 2.0.

Since we need a modern build tool, flexible enough to support Play original conventions and able to build Java and Scala projects, we have chosen to integrate sbt in Play 2.0. This, however, should not scare existing Play users who are happy with the simplicity of the original Play build. We are leveraging the same simple `play new`, `run`, `start` experience on top of an extensible model: Play 2.0 comes with a preconfigured build script that will just work for most users. On the other hand, if you need to change the way your application is built and deployed, the fact that a Play project is a standard sbt project gives you all the power you need to customize and adapt it.

This also means better integration with Maven projects out of the box, the ability to package and publish your project as a simple set of JAR files to any repository, and especially live compiling and reloading at development time of any depended project, even for standard Java or Scala library projects.

## 数据存储和模型的集成

‘数据存储’ 不再是与 ‘SQL 数据库’同步, 也许再也不会了. 很多新的数据存储模型变得越来越流行, 它们可以为不同的场景提供不同的特性. 基于这种情况，导致很难让Play预知开发者将要使用什么样的数据模型和存储方式，因此也很难使用简单的API把这些不同的形式抽象出来，传统的Play数据模型变得不再有意义.

在 Play 2.0 中, 我们想要把使用任何一种数据存储方式都变得简单, ORM, 或者任何其他能够与Web应用集成的任何数据模型. 我们是想通过一种简单的方式提供最基本的数据处理工具来帮助管理数据, 如管理数据库连接. 但我们同时希望维持Play框架的一站式架构风格，因此我们提供了一些默认的工具给那些没有特殊需求的人去访问传统数据库. 这就是为什么Play默认包含 Ebean, JPA 和 Anorm 这种API.