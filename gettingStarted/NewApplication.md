# Creating a new application

Once the Play is [[correctly installed | Installing]], you can create a new application.

## Create a new application with the play script.

The easiest way to create a new application is to use the `play new` command.

```bash
$ play new myFirstApp
```

It will ask a few question:

- The application name (just for information, this name will be used later in several messages).
- The template to use for this new application. You can choose over a default Scala application, a default Java application, or an empty application.

[[playNew.png]]

> Note that choosing a template at this point does not imply that you can't change of main language later. You can create a new application using the default Java application template and start adding some Scala code later.

Once the application is created you can use the `play` command again to enter the [[Play 2.0 console | PlayConsole]].

```bash
$ cd myFirstApp
$ play
```

## Create a new application without having Play installed.

You can also create a new Play application without installing Play, using sbt. 

> First install [[sbt 0.11.0 | https://github.com/harrah/xsbt/wiki/Getting-Started-Setup]].

Then create a new directory for your new application, and configure your sbt build script, by adding:

In `project/plugins.sbt`:

```scala
resolvers ++= Seq(
  "Maven Repository" at "http://repo1.maven.org/maven2/",
  "Typesafe Repository" at "http://repo.typesafe.com/typesafe/releases/"
)
 
libraryDependencies += "play" %% "play" % "2.0-beta"
``` 

And in `project/Build.scala`:

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

**sbt** will load your project and fetch every dependencies.

