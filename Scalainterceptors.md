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

#intercepting on method level

It's also possible to intercept a specific Action logic:

```scala
//return Results.Forbidden or the passed Action
def isActive(productId: String)(f: Request[AnyContent] => Result) = {
   Products.get(id).map(_ => f).getOrElse( Results.Forbidden) 
}

//then in a controller
def listOrders(productId: String) = isActive {
  Action{request => 
    val orders = orders.filter(e.get(productId).isDefinedAt)
    Ok(html.orders.list(orders))
  }
}
```

> **Next:** [[Testing your application | ScalaTest]]