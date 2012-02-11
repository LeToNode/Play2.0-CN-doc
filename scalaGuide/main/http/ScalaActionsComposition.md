# Action composition

This chapter introduce several ways of defining generic action functionality.

## Basic action composition

Let’s start with the simple example of a logging decorator: we want to log each call to this action.

The first way is not to define our own Action, but just to provide a helper method building a standard Action:

```
def LoggingAction(f: Request[AnyContent] => Result): Action[AnyContent] = {
  Action { request =>
    Logger.info("Calling action")
    f(request)
  }
}
```

That you can use as:

```
def index = LoggingAction { request =>
  Ok("Hello World")    
}
```

This is simple but it works only with the default `parse.anyContent` body parser as we don't have a way to specify our own body parser. We can of course define an additional helper method:

```
def LoggingAction[A](bp: BodyParser[A])(f: Request[A] => Result): Action[A] = {
  Action(bp) { request =>
    Logger.info("Calling action")
    f(request)
  }
}
```

And then:

```
def index = LoggingAction(parse.text) { request =>
  Ok("Hello World")    
}
```

## Wrapping existing actions

Another way is to define our own `LoggingAction` that would be a wrapper over another `Action`:

```
case class Logging[A](action: Action[A]) extends Action[A] {
  
  def apply(request: Request[A]): Result = {
    Logger.info("Calling action")
    action(request)
  }
  
  lazy val parser = action.parser
}
```

Now you can use it to wrap any other action value:

```
def index = Logging { 
  Action { 
    Ok("Hello World")
  }
}
```

Note that it will just re-use the wrapped action body parser as is, so you can of course write:

```
def index = Logging { 
  Action(parse.text) { 
    Ok("Hello World")
  }
}
```

> Another way to write the same thing but without defining the `Logging` class, would be:
> 
> ```
> def Logging[A](action: Action[A]): Action[A] = {
>   Action(action.parser) { request =>
>     Logger.info("Calling action")
>     action(request)
>   }
> }
> ```

## A more complicated example

Let’s look at the more complicated but common example of an authenticated action. The main problem is that we need to pass the authenticated user to the wrapped action and to wrap the original body parser to perform the authentication.

```
def Authenticated[A](action: User => Action[A]): Action[A] = {
  
  // Let's define an helper function to retrieve a User
  def getUser(request: RequestHeader): Option[User] = {
    request.session.get("user").flatMap(u => User.find(u))
  }
  
  // Wrap the original BodyParser with authentication
  val authenticatedBodyParser = parse.using { request =>
    getUser(request).map(u => action(u).parser).getOrElse {
      parse.error(Unauthorized)
    }          
  }
  
  // Now let's define the new Action
  Action(authenticatedBodyParser) { request =>
    getUser(request).map(u => action(u)(request)).getOrElse {
      Unauthorized
    }
  }
  
}
```

You can use it like this:

```
def index = Authenticated { user =>
  Action { request =>
    Ok("Hello " + user.name)      
  }
}
```

> **Note:** There is already an `Authenticated` action in `play.api.mvc.Security.Authenticated` with a better implementation than this example.

## Another way to create the Authenticated action

Let's see how to write the previous example without wrapping the whole action and without authenticating the body parser:

```
def Authenticated(f: (User, Request[AnyContent]) => Result) = {
  Action { request =>
    request.session.get("user").flatMap(u => User.find(u)).map { user =>
      f(user, request)
    }.getOrElse(Unauthorized)      
  }
}
```

To use this:

```
def index = Authenticated { (user, request) =>
   Ok("Hello " + user.name)    
}
```

A problem here is that you can't mark the `request` parameter as `implicit` anymore. You can solve that using currying:

```
def Authenticated(f: User => Request[AnyContent] => Result) = {
  Action { request =>
    request.session.get("user").flatMap(u => User.find(u)).map { user =>
      f(user, request)
    }.getOrElse(Unauthorized)     
  }
}
```

And now:

```
def index = Authenticated { user => implicit request =>
   Ok("Hello " + user.name)    
}
```

Another (probably simpler) way is to define our own subclass of `Request` as `AuthenticatedRequest` (so we are merging both parameters into a single one):

```
class AuthenticatedRequest(
  val user: User, request: Request[AnyContent]
) extends WrappedRequest(request)

def Authenticated(f: AuthenticatedRequest => Result) = {
  Action { request =>
    request.session.get("user").flatMap(u => User.find(u)).map { user =>
      f(AuthenticatedRequest(user, request))
    }.getOrElse(Unauthorized)            
  }
}
```

And then:

```
def index = Authenticated { implicit request =>
   Ok("Hello " + request.user.name)    
}
```

We can of course extend this last example and make it more generic by allowing to specify a body parser:

```
class AuthenticatedRequest[A](
  val user: User, request: Request[A]
) extends WrappedRequest(request)

def Authenticated[A](p: BodyParser[A])(f: AuthenticatedRequest[A] => Result) = {
  Action(p) { request =>
    request.session.get("user").flatMap(u => User.find(u)).map { user =>
      f(AuthenticatedRequest(user, request))
    }.getOrElse(Unauthorized)      
  }
}

// Overloaded method to use the default body parser
def Authenticated(f: AuthenticatedRequest[AnyContent] => Result) = {
  Authenticated(parse.anyContent)(f)
}
```

> **Next:** [[Asynchronous HTTP programming | ScalaAsync]]