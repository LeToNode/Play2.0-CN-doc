# Intercepting requests

## Overriding onRequest

One another important aspect of  the ```GlobalSettings``` class is that it provides a way to intercept requests and execute some business logic before a request is being dispatched to an Action. 

Let's see this in action:

```java
import play.*;

public class Global extends GlobalSettings {

@Override
public Action onRequest(Request request, Method actionMethod) {
   System.out.println("before each request..." + request.toString());
   return super.onRequest(request.actionMethod);
}

}
```

> **Next:** [[Testing your application | JavaTest]]