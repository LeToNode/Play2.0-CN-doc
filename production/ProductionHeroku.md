# Deploying to Heroku

[[Heroku | http://www.heroku.com/]] is a cloud application platform – a new way of building and deploying web apps.

## Add the Procfile

Heroku needs a special file in the application root called `Procfile`, create it with this simple content:

```txt
web: target/start
```

## Store Your App in Git

Just init a git repository for your application:

```bash
$ git init
$ git add .
$ git commit -m "init"
```

## Create a new application on heroku

> Note that you need an Heroku account, and to install the heroku gem before.

```bash
$ heroku create --stack cedar
Creating warm-frost-1289... done, stack is cedar
http://warm-1289.herokuapp.com/ | git@heroku.com:warm-1289.git
Git remote heroku added
```

## Deploy your application

To deploy your application on heroku, you just need to push it into the `heroku` remote:

```bash
$ git push heroku master
Counting objects: 34, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (20/20), done.
Writing objects: 100% (34/34), 35.45 KiB, done.
Total 34 (delta 0), reused 0 (delta 0)

-----> Heroku receiving push
-----> Scala app detected
-----> Building app with sbt v0.11.0
-----> Running: sbt clean compile stage
       ...
```

Heroku will run `sbt clean compile stage` to prepare your application. At the first deployment all dependencies will be fetched from the internet, taking a while to complete (it will stay cached for the next deployments).

## Check that your application is deployed

```bash
$ heroku ps
Process       State               Command
------------  ------------------  ----------------------
web.1         up for 10s          target/start 
```

```bash
$ heroku logs
2011-08-18T00:13:41+00:00 heroku[web.1]: Starting process with command `target/start`
2011-08-18T00:14:18+00:00 app[web.1]: Starting on port:28328
2011-08-18T00:14:18+00:00 app[web.1]: Started.
2011-08-18T00:14:19+00:00 heroku[web.1]: State changed from starting to up
...
```

## Running play commands remotely

Cedar allows you to launch a REPL process attached to your local terminal for experimenting in your app’s environment:

```bash
$ heroku run sbt play
```
