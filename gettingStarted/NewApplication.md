# 创建一个新的应用

## 使用play命令创建新应用

创建新应用最简单的方法是使用 `play new` 命令.

```bash
$ play new myFirstApp
```

This will ask for some information.

- The application name (just for display, this name will be used later in several messages).
- The template to use for this application. You can choose either a default Scala application, a default Java application, or an empty application.

[[images/playNew.png]]

> Note that choosing a template at this point does not imply that you can’t change language later. For example, you can create a new application using the default Java application template and start adding Scala code whenever you like.

Once the application has been created you can use the `play` command again to enter the [[Play 2.0 console | PlayConsole]].

```bash
$ cd myFirstApp
$ play
```

## Create a new application without having Play installed

You can also create a new Play application without installing Play, by using sbt. 

> First install [[sbt 0.11.2 | https://github.com/harrah/xsbt/wiki/Getting-Started-Setup]] if needed.

Just create a new directory for your new application and configure your sbt build script with two additions.

In `project/plugins.sbt`, add:

```scala
// The Typesafe repository 
resolvers += "Typesafe repository" at "http://repo.typesafe.com/typesafe/releases/"

// Use the Play sbt plugin for Play projects
addSbtPlugin("play" % "sbt-plugin" % "2.0")
```

Be sure to replace `2.0` here by the exact version you want to use. If you want to use a snapshot version, you will have to specify this additional resolver: 

```
// Typesafe snapshots
resolvers += "Typesafe Snapshots" at "http://repo.typesafe.com/typesafe/snapshots/"
```

In `project/Build.scala`:

```scala
import sbt._
import Keys._
import PlayProject._
 
object ApplicationBuild extends Build {
 
  val appName         = "My first application"
  val appVersion      = "1.0"
 
  val appDependencies = Nil
 
  val main = PlayProject(
    appName, appVersion, appDependencies, mainLang = SCALA
  ) 
 
}
```

You can then launch the sbt console in this directory:

```bash
$ cd myFirstApp
$ sbt
```

sbt will load your project and fetch the dependencies.

> **Next:** [[Anatomy of a Play 2.0 application | Anatomy]]