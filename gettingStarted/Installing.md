# Installing Play 2.0 beta

## Prerequisites

To run the Play framework, you need [[JDK 6 or later| http://www.oracle.com/technetwork/java/javase/downloads/index.html]]. If you wish to build Play from source, you will need the [[Git source control client | http://git-scm.com/]] to fetch the source code.

> If you are using MacOS, Java is built-in. If you are using Linux, make sure to use either the Sun JDK or OpenJDK (and not gcj which is the default Java command on many Linux distros). If you are using Windows, just download and install the latest JDK package.

Be sure to have the `java` and `javac` commands in the current path (you can check this by typing `java -version` and `javac -version` at the shell prompt). 

## Download the binary package

Download the [[Play 2.0 preview binary package | http://download.playframework.org/releases/play-2.0-preview.zip]] and extract the archive. 

## Add the play script in your PATH

For convenience, you should add the framework installation directory to your system PATH. On UNIX system you should add something like that somewhere:

```bash
export PATH=$PATH:/path/to/play20
```

On windows system it should be set somewhere in the global environment variables.

> If you’re on UNIX, make sure that the play script is executable (otherwise do a `chmod +x play`). 

## Check that the play command is available

From a shell, launch the `play help` command. 

```bash
$ play help
```

If everything is properly installed, you should see the basic help:

[[play.png]]

## Building from sources

To benefit from the latest improvements and bug fixes after the initial beta release, you may want to compile Play 2.0 from sources. You’ll need a [[Git client | http://git-scm.com/]] to fetch the sources.

From the shell, first checkout the Play 2.0 sources:

```bash
$ git clone git://github.com/playframework/Play20.git
```

Then go to the `Play20/framework` directory and launch the `build` script to enter the sbt build console:

```bash
$ cd Play20/framework
$ ./build
```

Once in the sbt console, run `build-repository` to compile and build everything. This will also create the local Ivy repository containing all of the required dependencies.

> Note that you don’t need to install sbt yourself: Play 2.0 embeds its own version (currently sbt 0.11.0).

If you want to make changes to the code you can use `compile` and `publish-local` to rebuild the framework.