# Creating a new application

## Create a new application with the play command

The easiest way to create a new application is to use the `play new` command.

```bash
$ play new myFirstApp
```

This will ask for some information.

- The application name (just for display, this name will be used later in several messages).
- The template to use for this application. You can choose either a default Scala application, a default Java application, or an empty application.

[[playNew.png]]

> Note that choosing a template at this point does not imply that you can’t change the main language later. For example, you can create a new application using the default Java application template and start adding Scala code at any point.

Once the application has been created you can use the `play` command again to enter the [[Play 2.0 console | PlayConsole]].

```bash
$ cd myFirstApp
$ play
```

## Create a new application without having Play installed

You can also create a new Play application without installing Play, by using sbt. 

> First install [[sbt 0.11.0 | https://github.com/harrah/xsbt/wiki/Getting-Started-Setup]] if needed.

Just create a new directory for your new application and configure your sbt build script with two additions.

In `project/plugins.sbt`, add:

```scala
resolvers ++= Seq(
  "Maven Repository" at "http://repo1.maven.org/maven2/",
  "Typesafe Repository" at "http://repo.typesafe.com/typesafe/releases/"
)

libraryDependencies += "play" %% "play" % "2.0-beta"
```

In `project/Build.scala`:

```scala
import sbt._
import Keys._
 
object ApplicationBuild extends Build {
 
  val appName         = "My first application"
  val appVersion      = "1.0"
 
  val appDependencies = Nil
 
  val main = PlayProject(appName, appVersion, appDependencies)
 
}
```

You can then launch the sbt console in this directory:

```bash
$ cd myFirstApp
$ sbt
```

sbt will load your project and fetch the dependencies.
