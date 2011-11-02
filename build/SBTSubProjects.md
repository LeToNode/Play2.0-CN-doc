# Working with sub projects

A complex project is not necessarily composed of a single Play application. You can want to split a large project into several smaller application, or even extract some logic in a standard Java or Scala library that has nothing to do with a Play application.

## Adding a simple library sub project

You can make your application depend of a simple library project. Just add another sbt project definition in your `project/Build.scala` build file:

```
import sbt._
import Keys._
import PlayProject._

object ApplicationBuild extends Build {

  val appName         = "my-first-application"
  val appVersion      = "1.0"

  val appDependencies = Seq()
  
  val mySubProject = Project("my-library", file("modules/myLibrary"))

  val main = PlayProject(appName, appVersion, appDependencies).dependsOn(mySubProject)
}
```

Here I have defined a sub project in the `modules/myLibrary` folder of my application. This sub project is a standard sbt project, using the default layout:

```
modules
  `-- myLibrary
        `-- src
              `-- main
                    `-- java
                    `-- scala 
```

When you have a sub project enabled in your build, you can focus on this project and compile/test/run it individually. Just use the `projects` command in the Play console prompt to display all projects:

```
[my-first-application] $ projects
[info] In file:/Volumes/Data/gbo/myFirstApp/
[info] 	 * my-first-application
[info] 	   my-library
```

To change the current project use the `project` command:

```
[my-first-application] $ project my-library
[info] Set current project to my-library (in build file:/Volumes/Data/gbo/myFirstApp/)
>
```



