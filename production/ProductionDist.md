# Creating a standalone version of your application

## Using the dist task

The preferred way to deploy a Play 2.0 application is to retrieve the source (typically via a git workflow) on the server and to use either `play start` or `play stage` to start it in place.

However sometimes you need to build a binary version of your application and deploy it to the server without any dependencies to Play itself. To do that, you can use the `dist` task.

In the Play console, simply type `dist`:

```bash
[My first application] $ dist
```

[[dist.png]]

It will produce a zip containing all jar files needed to run your application in the `target` folder of your application, the content of the zip file is organized as:

```
my-first-application-1.0
  `-- conf
        `-- application.conf
  `-- lib
        `-- *.jar
  `-- start
```

You can use the generated `start` script to run your application.