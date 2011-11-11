# Starting your application in production mode

## Using the start command

The easiest way to start an application in production mode is to use the `start` command from the Play console. However it requires that you have a version of the Play 2.0 framework installed on the server.

> Note that the `run` command is intended to be used in development mode and should never be used to run an application in production. For each request a complete check is handled by sbt.

```bash
[My first application] $ start
```

[[start.png]]

When you run the `start` command, Play will fork a new JVM and run the default Netty HTTP server. The standard output stream is redirected to the Play console, so you can monitor its status.

> The process id of the server is displayed at bootstrap and written in the `RUNNING_PID` file. To kill a running Play server, sending a `SIGTERM` to the process is enough to properly shutdown the application.

If you press `Ctrl+D`, the Play console will quit, but the created server process will continue running in background. The standard output stream of the forked JVM is then closed, and logs can be retrieved from the `logs/application.log` file.

If you press `Ctrl+C`, you will kill both JVM: the Play console and the forked Play server. 

Alternatively you can directly use `play start` at your OS command prompt:

```bash
$ play start
```

This does the same thing.

## Using the stage task

The problem with the `start` command is that it starts the application interactively. Meaning that human interaction is needed - and `Ctrl+D` is required to detach the process. If you need to automate deployment, this solution is not really convenient.

You can then use the `stage` task to prepare your application to be run in place. The typical command for preparing a project to be run in place is:

```bash
$ play clean compile stage
```

[[stage.png]]

This will clean and compile your application and retrieve all the required dependencies to the `target/staged` directory. It will also create a `target/start` script that will run the Play server.

With this you can directly start your application using:

```bash
$ target/start
```

The generated `start` script is very simple - in fact, you could even directly issue the `java` command yourself if needed.

If you don’t have Play installed on the server, you can use sbt to do the same:

```bash
$ sbt clean compile stage
```
