# Introduction à Play 2.0

Depuis 2007, nous avons travaillé à rendre le développement d'applications web en Java plus facile. Play a commencé comme un projet interne à [[Zenexity|http://www.zenexity.com]] et a été fortement influencé par notre façon de faire des projets web: focalisé sur la productivité des développeurs, respectant l'architecture web et utilisant une approche novatrice tenant compte des conventions de packaging dès le début - rompant avec meilleures pratiques JEE ainsi appelées quand cela avait du sens.

En 2009, nous avons décidé de partager ces idées avec la communauté en tant que projet open source. Le retour immédiat a été extrêmement positif et le projet a beaucoup gagné en dynamisme. Aujourd'hui - après deux ans de développement actif - Play possède plusieurs versions, une communauté active de 4000 personnes et un nombre croissant d'applications en production partout dans le monde.

Ouvrir un projet au monde signifie certainement davantage de retour, mais signifie également découvrir et apprendre de nouveaux cas d'utilisation, se voir demander de nouvelles fonctionnalités et voir exhumer des bugs qui n'était pas spécifiquement pris en compte dans la conception initiale et ses hypothèses. Durant les deux ans du travail sur Play en tant que projet open source, nous avons travaillé à corriger ce type de problèmes, ainsi qu'à intégrer de nouvelles fonctionnalités pour gérer une plage plus étendue de scénarios. Au fur et à mesure que le projet grandissait, nous avons appris beaucoup de la communauté et de notre propre expérience - acquise en utilisant Play dans de plus en plus de projets de plus en plus complexes et de plus en plus diversifiés.

Pendant ce temps, la technologie et le web ont continué à évoluer. Le web est devenu le point central de toutes les applications. Les technologies HTML, CSS et Javascript ont évoluées rapidement, ne permettant presque plus à un framework côté serveur de suivre. L'architecture web entière a rapidement évoluée vers le traitement en temps réel et les exigences émergentes des profils des projets actuels signifie que le SQL ne s'impose plus comme la technologie de stockage exclusive. Au niveau du langage de programmation nous avons été témoins de certains changements monumentaux, certains langages de JVM, dont Scala, gagnant en popularité.

C'est pourquoi nous avons créé Play 2.0, un nouveau framework web pour une nouvelle ère.

## Conçu pour la programmation asynchrone

Les applications web actuelles intègrent davantage de données temps réel concurrentes, si bien que les frameworks web doivent gérer un modèle de programmation HTTP asynchrone. Play avait été initialement conçu pour gérer des applications web classiques avec beaucoup de requêtes à courte durée de vie. Mais maintenant, le modèle d'événement est le moyen d'aller vers des connexions persistantes via Comet, des requêtes longues et les WebSockets.

Play 2.0 est architecturé depuis le début sur l'idée que chaque requête possède potentiellement une longue durée de vie. Mais ce n'est pas tout: nous avons également besoin d'un moyen puissant de planifier et d'exécuter des tâches de longue durée. Le modèle basé sur les Acteurs est sans discussion le meilleur modèle actuel pour gérer les systèmes fortement concurrents et la meilleure implémentation disponible de ce modèle à la fois pour Java et Scala est Akka - c'est pourquoi il a été retenu. Play 2.0 fournit une prise en charge native de Akka pour les applications Play, rendant possible d'écrire des systèmes hautement distribués.

## aFocused on type safety

One benefit of using a statically-typed programming language for writing Play applications is that the compiler can check parts of your code. This is not only useful for detecting mistakes early in the development process, but it also makes it a lot easier to work on large projects with a lot developers involved.

Adding Scala to the mix for Play 2.0, we clearly benefit from even stronger compiler guarantees - but that’s not enough. In Play 1.x, the template system was dynamic, based on the Groovy language, and the compiler couldn’t do much for you. As a result, errors in templates could only be detected at run-time. The same goes for verification of glue code with controllers.

In version 2.0, we really wanted to push this idea of having Play check most of your code at compilation time further. This is why we decided to use the Scala-based template engine as the default for Play applications - even for developers using Java as the main programming language. This doesn’t mean that you have to become a Scala expert to write templates in Play 2.0, just as you were not really required to know Groovy to write templates in Play 1.x.

In templates, Scala is mainly used to navigate your object graph in order to display relevant information, with a syntax that is very close to Java’s. However, if you want to unleash the power of Scala to write advanced templates abstractions, you will quickly discover how Scala, being expression-oriented and functional, is a perfect fit for a template engine.

And that’s not only true for the template engine: the routing system is also fully type-checked. Play 2.0 checks your routes’ descriptions, and verifies that everything is consistent, including the reverse routing part.

A nice side effect of being fully compiled is that the templates and route files will be easier to package and reuse. You also get a significant performance gain on these parts at run-time.

## Native support for Java and Scala

Early in the Play project’s history, we started exploring the possibility of using the Scala programming language for writing Play applications. We initially introduced this work as an external module, to be able to experiment freely without impacting the framework itself.

Properly integrating Scala into a Java-based framework is not trivial. Considering Scala’s compatibility with Java, one can quickly achieve a first naive integration that simply uses Scala’s syntax instead of Java’s. This, however, is certainly not the optimal way of using the language. Scala is a mix of true object orientation with functional programming. Leveraging the full power of Scala requires rethinking most of the framework’s APIs.

We quickly reached the limits of what we can do with Scala support as a separate module. Initial design choices we made in Play 1.x, relying heavily on Java reflection API and byte code manipulation, have made it harder to progress without completely rethinking some essential parts of Play’s internals. Meanwhile, we have created several awesome components for the Scala module, such as the new type-safe template engine and the brand new SQL access component Anorm. This is why we decided that, to fully unleash the power of Scala with Play, we would move Scala support from a separate module to the core of Play 2.0, which is designed from the beginning to natively support Scala as a programming language.

Java, on the other hand, is certainly not getting any less support from Play 2.0; quite the contrary. The Play 2.0 build provides us with an opportunity to enhance the development experience for Java developers. Java developers get a real Java API written with all the Java specificity in mind.

## Powerful build system

From the beginning of the Play project, we have chosen a fresh way to run, compile and deploy Play applications. It may have looked like an esoteric design at first, but it was crucial to providing an asynchronous HTTP API instead of the standard Servlet API, short feedback cycles through live compilation and reloading of source code during development, and promoting a fresh packaging approach. Consequently, it was difficult to make Play follow the standard JEE conventions.

Today, this idea of container-less deployment is increasingly accepted in the Java world. It’s a design choice that has allowed the Play framework to run natively on platforms like Heroku, which introduced a model that we consider the future of Java application deployment on elastic PaaS platforms.

Existing Java build systems, however, were not flexible enough to support this new approach. Since we wanted to provide straightforward tools to run and deploy Play applications, in Play 1.x we created a collection of Python scripts to handle build and deployment tasks.

Meanwhile, developers using Play for more enterprise-scale projects, which require build process customization and integration with their existing company build systems, were a bit lost. The Python scripts we provide with Play 1.x are in no way a fully-featured build system and are not easily customizable. That’s why we’ve decided to go for a more powerful build system for Play 2.0.

Since we need a modern build tool, flexible enough to support Play original conventions and able to build Java and Scala projects, we have chosen to integrate sbt in Play 2.0. This, however, should not scare existing Play users who are happy with the simplicity of the original Play build. We are leveraging the same simple `play new`, `run`, `start` experience on top of an extensible model: Play 2.0 comes with a preconfigured build script that will just work for most users. On the other hand, if you need to change the way your application is built and deployed, the fact that a Play project is a standard sbt project gives you all the power you need to customize and adapt it.

This also means better integration with Maven projects out of the box, the ability to package and publish your project as a simple set of JAR files to any repository, and especially live compiling and reloading at development time of any depended project, even for standard Java or Scala library projects.

## Datastore and model integration

‘Data store’ is no longer synonymous with ‘SQL database’, and probably never was. A lot of interesting data storage models are becoming popular, providing different properties for different scenarios. For this reason it has become difficult for a web framework like Play to make bold assumptions regarding the kind of data store that developers will use. A generic model concept in Play no longer makes sense, since it is almost impossible to abstract over all these kinds of technologies with a single API.

In Play 2.0, we wanted to make it really easy to use any data store driver, ORM, or any other database access library without any special integration with the web framework. We simply want to offer a minimal set of helpers to handle common technical issues, like managing the connection bounds. We also want, however, to maintain the full-stack aspect of Play framework by bundling default tools to access classical databases for users WHO don’t have specialized needs, and that’s why Play 2.0 comes with built-in relational database access libraries such as Ebean, JPA and Anorm.
