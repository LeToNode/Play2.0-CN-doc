# Intercepting requests

One another important aspect of  the ```Global``` object is that it provides a way to intercept requests and execute some business logic before a request is being dispatched to an Action. 

> **Tip:** this hook can be also used for hijacking requests, allowing developers to plugin their own request routing mechanism. 

Let's see how to intercept requests:

```scala
import play.api._

object Global extends GlobalSettings {
  def onRouteRequest(request: RequestHeader): Option[Handler] = {
     println("executed before every request:" + request.toString)
     super.onRouteRequest(request)
  }
}
```

#intercepting only specific Action methods

Instead of intercepting each and every request, it's also possible to decorate only specific Action methods:

```scala
//return Results.Forbidden or the passed Action
def isActive(productId: String)(f: Request[AnyContent] => Result) = {
   Products.get(id).map(_ => f).getOrElse( Results.Forbidden) 
}

//then in a controller
def listOrders(productId: String) = isActive(productId) {
  Action{request => 
    val orders = orders.filter(e.get(productId).isDefinedAt)
    Ok(html.orders.list(orders))
  }
}
```

> **Next:** [[Testing your application | ScalaTest]]