# Intercepting requests

One another important aspect of  the ```GlobalSettings``` class is that it provides a way to intercept requests and execute some business logic before a request is being dispatched to an Action. 

Let's see this in action:

```java
import play.*;

public class Global extends GlobalSettings {

@Override
public Action onRequest(Request request, Method actionMethod) {
   System.out.println("before each request..."+request.toString());
   return super.onRequest(request,actionMethod);;
}

}
```

#Decorating Action methods with the ```With``` annotations

Alternatively, it's also possible to decorate an Action method with another one using the ```play.mvc.With``` annotation.

```java
//Interceptor.java
import play.mvc.Result;
import play.mvc.Action.Simple;
import play.mvc.Http.Context;

public class Interceptor extends Simple {
    
    public static String customState = "";
    
    @Override
    public Result call(Context ctx) throws Throwable {
        customState = "intercepted";
        return delegate.call(ctx);
    }

}

//controllers/Application.java
  import play.*;
  import play.mvc.*;

  public class Application extends Controller {
    
    @With(Interceptor.class)
    public static Result myActionMethod {
        return ok(Interceptor.customState);
   }

 }
```

> **Next:** [[Testing your application | JavaTest]]