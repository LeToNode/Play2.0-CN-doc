# Intercepting requests

One another important aspect of  the ```Global``` object is that it provides a way to intercept requests and execute some business logic before a request is being dispatched to an Action. 

> **Tip:** this hook can be also used for hijacking requests, allowing developers to plugin their own request routing mechanism. 

Let's see how to intercept requests:

```scala
import play.api._

object Global extends GlobalSettings {
  def onRouteRequest(request: RequestHeader): Option[Handler] = {
     println("before request, yay:" + request.toString)
     super.onRouteRequest(request)
  }
}
```

> **Next:** [[Testing your application | ScalaTest]]