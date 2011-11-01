# Installing Play 2.0 beta

## Prerequisites

To run the Play framework, you need a [[JDK 6 or later| http://www.oracle.com/technetwork/java/javase/downloads/index.html]]. If you wish to build Play from source, you will need the [[Git source control client | http://git-scm.com/]] to fetch the source code.

> If you are using MacOS, Java is just built-in. If you are using Linux, make sure to use either the Sun JDK or OpenJDK (and not gcj which is the default Java command on many Linux distros). If you are using Windows, just download and install the latest JDK package.

Be sure to have **java** and **javac** in the current path (you can check by typing `java -version` and `javac -version` at the shell prompt). 

## Download the binary package

Download the [[Play 2.0 beta binary package | http://download.playframework.org/releases/play-2.0-beta.zip]] and extract the archive. For convenience, you should add the framework installation directory to your system PATH. If you’re on UNIX, make sure that the play script is runnable (otherwise simply do a `chmod +x play`). That’s all.

## Check that the play command is available

From a shell, try to launch the `play help` command. 

```bash
$ play help
```

If everything is properly installed, you should see the very basic help:

[[play.png]]

## Building from sources

To benefit from the latest improvements and bug fixes after the first beta release, you may want to compile Play 2.0 from sources. You’ll need a [[Git client | http://git-scm.com/]] to fetch the sources.

From the shell, first checkout the Play 2.0 sources:

```bash
$ git clone git://github.com/playframework/Play20.git
```

Then enter into `Play20/framework` and launch the `build` script to enter the sbt build console:

```bash
$ cd Play20/framework
$ ./build
```

Once in the sbt console, run `build-repository` to compile and build everything. It will also create the local ivy repository containing every needed dependencies.

> Note that you don't need to install your own sbt version. Play 2.0 embed its own version of sbt (currently using sbt 0.11.0).

If you want to make changes to the code you can use `compile` and `publish-local` to rebuild the framework.