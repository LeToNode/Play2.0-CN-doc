# Installing Play 2.0 beta

## Prerequisites

To run the Play framework, you need a [[JDK 6 or later| http://www.oracle.com/technetwork/java/javase/downloads/index.html]]. If you wish to build Play from source, you will need the [[Git source control client | http://git-scm.com/]] to fetch the source code.

> If you are using MacOS, Java is just built-in. If you are using Linux, make sure to use either the Sun-JDK or OpenJDK (and not gcj which is the default Java command on many Linux distros). If you are using Windows, just download and install the latest JDK package.

Be sure to have **java** and **javac** in the current path (enter `java -version` and `javac -version` to check). 

## Download the binary package

Download the [[Play 2.0 beta binary package | #]] and extract the archive. For convenience, you should add the framework installation directory to your system PATH. If you’re on UNIX, make sure that the play script is runnable (otherwise simply do a `chmod +x play`). That’s all.

## Check that the `play` command is available

From a shell, try to launch the `play help` command. If everything is properly installed, you should see the very basic help:

[[play.png]]