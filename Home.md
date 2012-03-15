# Play 2.0 中文文档

> Play 2.0是基于Java和Scala Web框架，集成了开发流行Web应用所需的全部组件的高开发效率Web应用框架。
>
> Play基于一种轻量级, 无状态, web友好的架构，通过使用Iteratee IO实现的模型，为我们提供了低资源消耗（如CPU、内存和线程等）的，高伸缩性的开发能力。

## 关于

1. [[Play 2.0，WEB框架的全新时代 | Philosophy]]

## 入门

1. [[Installing Play 2.0 | Installing]]
1. [[Creating a new application | NewApplication]]
1. [[Anatomy of a Play 2.0 application | Anatomy]]
1. [[Using the Play 2.0 console | PlayConsole ]]
1. [[Setting-up your preferred IDE | IDE]]
1. [[Sample applications | Samples]]

## Working with Play 2.0

1. [[Play 2.0 for Scala developers | ScalaHome]]
1. [[Play 2.0 for Java developers | JavaHome]]

## Detailed topics

1. [[The Build system | Build]]
    1. [[About sbt settings | SBTSettings]]
    1. [[Manage application dependencies | SBTDependencies]]
    1. [[Working with sub-projects | SBTSubProjects]]
1. [[Working with public assets | Assets]]
    1. [[Using CoffeeScript | AssetsCoffeeScript]]
    1. [[Using LESS CSS | AssetsLess]]
    1. [[Using Google Closure Compiler | AssetsGoogleClosureCompiler]]
1. [[Managing database evolutions | Evolutions]]
1. [[Configuration file syntax and features | Configuration]]
    1. [[Configuring the JDBC connection pool | SettingsJDBC]]
    1. [[Configuring the internal Akka system | AkkaCore]]
    1. [[Configuring logging | SettingsLogger]]
1. [[Deploying your application | Production]]
    1. [[Creating a standalone version of your application | ProductionDist]]
    1. [[Additional configuration | ProductionConfiguration]]
    1. [[Deploying to Heroku | ProductionHeroku]]
    1. [[Set-up a front-end HTTP server | HTTPServer]]

## Additional documentations

1. [[Scala|http://docs.scala-lang.org/]]
1. [[Akka|http://akka.io/docs/]]
1. [[sbt|http://www.scala-sbt.org/learn.html]]
1. [[Configuration|https://github.com/typesafehub/config]]
1. [[Logback|http://logback.qos.ch/documentation.html]]

## Hacking Play 2.0

1. [[Building Play 2.0 from source | BuildingFromSource]]
1. [[CI server at Cloudbees | CIServer]]
1. [[Repositories | Repositories]]
1. [[Issue tracker | Issues]]
1. [[Pull requests | PullRequests]]
1. [[Contributor guidelines | Guidelines]]

## Contributing to this documentation

> The documentation is freely editable on [[https://github.com/playframework/Play20/wiki]]. Feel free to fix mistakes directly in the pages. 
>
> However if you want to provide additional documentation, please create new pages and reference them from here. A member of the core team will take care of integrating it in the most appropriate section.

### To be integrated:

1. [[Missing parts | Missing]]
1. [[Tips and tricks | Tips]]