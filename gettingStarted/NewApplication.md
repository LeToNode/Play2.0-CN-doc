# Creating a new application

Once the Play is [[correctly installed | Installing]], you can create a new application.

## Create a new application with the play script.

The easiest way to create a new application is to use the `play new` command.

```bash
$ play new myFirstApp
```

It will ask a few question:

- The application name (just for information, this name will be used later in several messages).
- The template to use for this new application. You can choose over a default Scala application, a default Java application, an empty application.

[[playNew.png]]

> Note that choosing a template at this point does not imply that you can't change of main language latter. You can create a new application using the default Java application template and start adding some Scala code later.

Once the application is created you can use the `play` command again to enter the [[Play 2.0 console | PlayConsole]]

```bash
$ cd myFirstApp
$ play
```