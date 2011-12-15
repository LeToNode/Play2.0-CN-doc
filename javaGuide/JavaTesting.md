# Testing your application

_Tip: Java applications can be easily [tested with Scala](https://github.com/playframework/Play20/wiki/Scalatest) as well. In fact, writing tests is probably the easiest way to pick up a new language._

# Writing unit tests

Play comes with support for JUnit and Play context mocking. The idea is that in the `@Before` method we set-up the mock play application and then any method marked with `@Test` will be executed in this context. `injectGlobalMock` takes:

- a List of plugin references, for example `ArrayList<String> my new ArrayList<String>(); my.add("110:mymock.plugin.CachePlugin");`
- a `Map<String,String>` of configuration options (optionally, you can use a predefined in-memory database mock, as in the example below).

Let's see an example:

```java
public class ApplicationTest extends MockApplication{

  public ApplicationTest() {
    Logger.warn("starting ApplicationTest...");
  }
  @Before public void init() {
    injectGlobalMock(new ArrayList<String>(), MockData.dataSource());
  }

  @Test public void test() {
    //create mock request
    play.api.mvc.Request<RequestBody> req = toRequest(
    "/computers",
    "GET",
    new HashMap<String, String[]>(),
    new HashMap<String, String[]>(),
    "peter",
    "/computers",
    new HashMap<String, String[]>(),
    new HashMap<String, String>()
    );

    //set mock request into context
    Context.current.set(createJavaContext(req));

    //create Action
    play.api.mvc.Action action = toAction(
      controllers.Application.list(0, "name", "asc", "")
    );

    //execute action
    ResultData result = Extract.from(action.apply(req));

    assertEquals(result.body().toString().contains("574 computers found"),true);
  }
  @After public void after() {
   clearMock();
  }
}
```

[ApplicationTest.java](https://github.com/playframework/Play20/blob/master/samples/java/computer-database/test/ApplicationTest.java) provides a full example.

# Writing integration tests

The other approach is to fire up a full application and run an integration test against it using either [Selenium/WebDriver](http://seleniumhq.org/docs/03_webdriver.html) or [FluentLenium](https://github.com/MathildeLemee/FluentLenium) which is built on top of WebDrive. Let's see an example

```java
public class FunctionalTest extends FluentTest{
  
  public WebDriver webDriver = new HtmlUnitDriver();

  @Override
  public WebDriver getDefaultDriver() {
        return webDriver;
  }

  @Before public void init() {
    Logger.warn("starting FunctionalTest...");
    play.api.db.evolutions.OfflineEvolutions.applyScript("default");
  }

  @Test public void Test() {
     withNettyServer(new Runnable() {
          public void run() {
            goTo("http://localhost:9001");
            boolean cond =  pageSource().contains("574 computers found");
            assertEquals(cond, true);
          }});
  }

}
```

[FunctionalTest.java](https://github.com/playframework/Play20/blob/master/samples/java/computer-database/test/FunctionalTest.java) provides a full example. Consult [FluentLenium](https://github.com/MathildeLemee/FluentLenium) for more information.

