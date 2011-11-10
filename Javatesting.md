# Testing your application

_Tip: Java applications can be easily [tested with scala](https://github.com/playframework/Play20/wiki/Scalatest) as well. In fact, writing tests is probably the easiest to pick up any new languages._

# Writing unit tests
Play comes with support for Junit and mocking. The idea is that in the `@Before` method we setup the mock play application and the any method marked with `@Test` will be executed in this context. `injectGlobalMock` takes
- a List of plugin references, for example ArrayList<String> my new ArrayList<String>(); my.add("110:mymock.plugin.CachePlugin");"
- a `Map<String,String>` of configuration (optionally one can use a predefined in-memory database mock, just like in the example below)


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
a full example can be [found here](https://github.com/playframework/Play20/blob/master/framework/integrationtest/test/ApplicationTest.java)

# Writing integration tests

The other approach is to fire up a full application and run an integration test against it using [Selenium/WebDriver](http://seleniumhq.org/docs/03_webdriver.html). By default there are two drivers included in play's test scope: [HtmlUnitDriver](http://seleniumhq.org/docs/03_webdriver.html#htmlunit-driver) and [ChromeDriver](http://code.google.com/p/selenium/wiki/ChromeDriver#Overriding_the_Chrome_binary_location). Let's see an example using Htmlunit:

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
