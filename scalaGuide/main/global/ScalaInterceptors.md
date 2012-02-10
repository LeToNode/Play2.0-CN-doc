# Intercepting requests

## Overriding onRouteRequest

One another important aspect of  the ```Global``` object is that it provides a way to intercept requests and execute some business logic before a request is being dispatched to an Action. 

> **Tip:** this hook can be also used for hijacking requests, allowing developers to plugin their own request routing mechanism. 

Let's see how this works in practice:

```scala
import play.api._

//notice: this is in the default package
object Global extends GlobalSettings {

  def onRouteRequest(request: RequestHeader): Option[Handler] = {
     println("executed before every request:" + request.toString)
     super.onRouteRequest(request)
  }

}
```

Of course it's also possible to intercept only a specific method. This can be achieved via [[Actions composition | ScalaActionsComposition]]


> **Next:** [[Testing your application | ScalaTest]]