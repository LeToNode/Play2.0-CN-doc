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
    Action action = controllers.Application.index_java_cache();
    Result result = action.apply(new FakeRequest());
    assertEquals(result.toString().contains("200,Map(Content-Type -> text/html"),true);
  }
  @After public void after() {
   clearMock();
  }
}
```

[framework/integrationtest/test/ApplicationTest.java](https://github.com/playframework/Play20/blob/master/framework/integrationtest/test/ApplicationTest.java) provides a full example.

# Writing integration tests

The other approach is to fire up a full application and run an integration test against it using [Selenium/WebDriver](http://seleniumhq.org/docs/03_webdriver.html). By default there are two drivers included in Play's test scope: [HtmlUnitDriver](http://seleniumhq.org/docs/03_webdriver.html#htmlunit-driver) and [ChromeDriver](http://code.google.com/p/selenium/wiki/ChromeDriver). Here is an HtmlUnit example:

```java
package test;

import org.junit.*;
import static org.junit.Assert.assertEquals;
import play.test.*;
import org.openqa.selenium.htmlunit.HtmlUnitDriver;

public class ApplicationTest extends IntegrationTest {
@Test public void funcTest() {
   withNettyServer(new Runnable() {
     public void run() {
         HtmlUnitDriver driver = new HtmlUnitDriver();
         driver.get("http://localhost:9000");
         assertEquals(driver.getPageSource().contains("Hello world"), true);
     }
   });
  }
}
```
