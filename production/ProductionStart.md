# Starting your application in production mode

## Using the start command

The easiest way to start an application in production mode is to use the `start` command from the Play console. However it requires to have a version of the Play 2.0 framework installed on the server.

> Note that the `run` command is intended to be used in development mode and must never be used to run an application in production. For each request a complete check is handled by sbt.

[[start.png]]

When you run the `start` command, Play will fork a new JVM and run the default Netty HTTP server. The standard output stream is redirected to the Play console, so you can check that everything is ok.

> The process id of the server is displayed at bootsrap and written in the `RUNNING_PID` file. To kill a running Play server, sending a `SIGTERM` to the process is enough to properly shutdown the application.

If you press `Ctrl+D`, the Play console will quit, but the created server process will keep running in background. The standard output stream of the forked JVM is then closed, and logs can be retrieved in the `logs/application.log` file.

If you press `Ctrl+C`, you will kill both JVM: the Play console and the forked Play server. 