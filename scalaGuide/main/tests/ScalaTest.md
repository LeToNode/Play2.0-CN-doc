# Testing your application

Tests source files must be placed in the `test` folder of your application. You can run them from the play console using the `test` and `test-only` tasks.

## Using specs2

The default way to test a Play 2 application is by using [[specs2| http://etorreborre.github.com/specs2/]].

Unit specifications extend the `org.specs2.mutable.Specification` trait and are using the should/in format:

```
import org.specs2.mutable._

class HelloWorldSpec extends Specification {

  "The 'Hello world' string" should {
    "contain 11 characters" in {
      "Hello world" must have size(11)
    }
    "start with 'Hello'" in {
      "Hello world" must startWith("Hello")
    }
    "end with 'world'" in {
      "Hello world" must endWith("world")
    }
  }
}
```

## Running in a Fake application

If the code you want to test depends of a running application, you can easily create a `FakeApplication` on the fly:

```
"Computer model" should {

  "be retrieved by id" in {
    running(FakeApplication()) {
  
      val Some(macintosh) = Computer.findById(21)

      macintosh.name must equalTo("Macintosh")
      macintosh.introduced must beSome.which(dateIs(_, "1984-01-24"))  
  
    }
  }
}
```

You can also pass (or override) additional configuration to the fake application, or mock any plugin. For example to create a `FakeApplication` using a `default` in memory database:

```
FakeApplication(additionalConfiguration = inMemoryDatabase())
```

> **Next:** [[Writing functional tests | ScalaFunctionalTest]]