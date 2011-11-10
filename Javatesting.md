# Testing an application

_Tip: Java applications can be [tested with scala](https://github.com/playframework/Play20/wiki/Scalatest) as well. In fact writing tests is probably the easiest to pick up any new languages._

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
    injectGlobalMock(new ArrayList<String>(), MockData.dataSource());··
  }

  @Test public void Test() {
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