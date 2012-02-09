# Intercepting requests

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

# Decorating Action methods

Instead of intercepting each and every request, it's also possible to decorate only specific Action methods:

```scala
//app/util/Decorators.scala
package util
import com.example.Products

object Decorators {

  //return Results.Forbidden or the passed in Action
  def isActiveProduct(id: String)(f: Request[AnyContent] => Result) = {
     Products.current.get(productId).filter(_.isActive == true).map(_ => f).getOrElse( Results.Forbidden) 
  }

}

//app/controllers/Application.scala
package controllers

import play.api.mvc._
import com.example.Orders
import util.Decorators.isActiveProduct
object Application extends Controller {

  def listOrdersForProduct(id: String) = isActiveProduct(productId) {
     Action{request => 
       val orders = Orders.current.filter(e.get(productId).isDefinedAt)
       Ok(html.orders.list(orders))
    }
  }

}
```

> **Next:** [[Testing your application | ScalaTest]]