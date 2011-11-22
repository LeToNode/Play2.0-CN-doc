# Building on top of Play

_Note: This document is a work in progress_

While one can really take advantage of most play features while building an application from scratch using play. It's very easy to drop play into existing sbt/maven projects and with just a little work use it as an http REST library. Below you can see how to do that.

# Rolling your own

A ```Global``` object (extending ```GobalSettings```) in the global name space is the key wiring point. GobalSettings has a method ``` def onRouteRequest(request: RequestHeader): Option[Handler] ``` that's handling play's routing. 

One could easily provide an alternative implementation on top of this and start using play as an http library! See [this](https://github.com/typesafehub/play2-mini/blob/master/src/main/scala/com/typesafe/play/mini/Setup.scala) for an example.