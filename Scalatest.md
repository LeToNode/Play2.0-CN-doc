# Testing your Application

# Writing Specs
One way to test a Play 2 application is by writing [specs](http://etorreborre.github.com/specs2/). Since
each part of the application can be mocked, one can easily create a spec where the environment is completely controlled.

The `withApplication` helper takes:

* a list of plugin references, for example `List("600:mymock.cache.CachePlugin")`
* a `Map[String,String]` of application configuration (an in-memory datasource can be also used for testing databases in isolation)
* and finally a user-supplied block, which will be executed in the application context. 

Putting it all together:

```scala
object ApplicationSpec extends Specification {

  "an Application" should {
    "execute index" in {
        withApplication(Nil, MockData.dataSource) {
          val action = controllers.Application.index
          val result = action.apply(new FakeRequest)
          ...
        }
     }
   }
}
```
a full example can be found [here](https://github.com/playframework/Play20/blob/master/framework/integrationtest/test/ApplicationSpec.scala)

# Writing Integration Test
The other approach is to fire up a full application and run an integration test against it using [Selenium/WebDriver](http://seleniumhq.org/docs/03_webdriver.html). By default there are two drivers included in play's test scope: [HtmlUnitDriver](http://seleniumhq.org/docs/03_webdriver.html#htmlunit-driver) and [ChromeDriver](http://code.google.com/p/selenium/wiki/ChromeDriver). Let's see an example using Htmlunit:
```scala
object FunctionalSpec extends Specification {

"an Application" should {
  "pass functional test" in {
   withNettyServer{
      val driver = new HtmlUnitDriver()
      driver.get("http://localhost:9000")
      driver.getPageSource must contain ("Hello world")
   }   
  }
 }
}
```
full example can be found [here](https://github.com/playframework/Play20/blob/master/framework/integrationtest/test/FunctionalSpec.scala)

_Tip: since this is a very resource intensive way of testing, it's recommended to have only one integration test per application._