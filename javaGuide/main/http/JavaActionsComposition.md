# Actions composition

This chapter introduce several ways of defining generic functionalities for actions.

## Reminder about actions

Previously we said that an action was a Java method returning a `play.mvc.Result` value. Actually, Play manages internally actions as functions. Because Java doesn't support first class functions, an action provided by the Java API is an instance of `play.mvc.Action`:

```
public abstract class Action {
    
  public abstract Result call(Http.Context ctx);    
    
}
```

Play build a root action for you which is just calling the proper action method. It allows for more complicated action composition.

## Composing actions

You can compose the code provided by the action method with another `play.mvc.Action`, using the `@With` annotation:

```
@With(VerboseAction.class)
public static Result index() {
  return ok("It works!");
}
```

Here is the definition of the `VerboseAction`:

```
public static class VerboseAction extends Action.Simple {

  public Result call(Http.Context ctx) throws Throwable {
    Logger.info("Calling action for " + ctx);
    return delegate.call(ctx);
  }

}
```

At one point you need to delegate to the wrapped action using `delegate.call(...)`.

You also mix with several actions:

```
@With(Authenticated.class, Cached.class)
public static Result index() {
  return ok("It works!");
}
```

> **Note:**  ```play.mvc.Security.Authenticated``` and ```play.cache.Cached``` annotations and the corresponding predefined Actions are shipped with Play. You might want to consult with the relevant Javadoc pages for more information


## Defining custom action annotations

You can also mark action composition with your own annotation, that you need to annotate itself using `@With`:

```
@With(VerboseAction.class)
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Verbose {
   boolean value() default true;
}
```

You can then use your new annotation with an action method:

```
@Verbose(false)
public static Result index() {
  return ok("It works!");
}
```

Your `Action` definition retrieve the annotation as configuration:

```
public static class VerboseAction extends Action<Verbose> {

  public Result call(Http.Context ctx) {
    if(configuration.value) {
      Logger.info("Calling action for " + ctx);  
    }
    return delegate.call(ctx);
  }

}
```

## Annotating controllers

You can also put any action composition annotation directly on the `Controller` class. In this case it will be applied to all actions methods defined by this controller.

```
@Authenticated
public Admin extends Controller {
    
  ...
    
}
```

> **Next:** [[Asynchronous HTTP programming | JavaAsync]]