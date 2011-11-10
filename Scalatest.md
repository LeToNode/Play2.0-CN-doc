# Testing your Application

# Writing Specs
One way to test a play2 application is by writing [specs](http://etorreborre.github.com/specs2/). Since
each part of the application can be mocked, one can easily create a spec where the environment is completely controlled.
The `withAppliaction` helper takes 
- a list of plugin references (for example `List("600:mymock.cache.CachePlugin"`) 
- a Map[String,String] of app configuration (an in-memory datasource can be also used for testing databases in isolation) 
- and finally a user supplied block which will be executed in the application context. Putting it all together:

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

# Writing integration tests