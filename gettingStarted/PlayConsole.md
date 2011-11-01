# Using the Play 2.0 console

## Launching the console

The Play 2.0 console is a development console based on **sbt** that allow to manage the complete development cycle of a Play application.

To launche the console, enter any existing application directory and launch the `play` script:

```bash
$ cd /path/to/any/application
$ play 
```

[[console.png]]

## Getting help

Use the `help` command to get basic help about the available commands:

```bash
[My first application] $ help
```

## Running the server in development mode

To run the current application in development mode, use the `run` command:

```bash
[My first application] $ run
```

[[consoleRun.png]]

In this mode, the server will be launched with the auto-reload feature enabled, meaning that for each request Play will check your project and recompile required sources. If needed the application will restart automatically.

To stop the server, you can press the `Crtl + D` key, and you will come back to the Play console prompt.