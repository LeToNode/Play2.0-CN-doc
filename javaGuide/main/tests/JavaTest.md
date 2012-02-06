# Testing your application

Tests source files must be placed in the `test` folder of your application. You can run them from the play console using the `test` and `test-only` tasks.

## Using specs2

The default way to test a Play 2 application is by using [[JUnit| http://www.junit.org/]].

```
package test;

import org.junit.*;

import play.mvc.*;
import play.test.*;
import play.libs.F.*;

import static play.test.Helpers.*;
import static org.fest.assertions.Assertions.*;

public class SimpleTest {

    @Test 
    public void simpleCheck() {
        int a = 1 + 1;
        assertThat(a).isEqualTo(2);
    }
```

## Running in a Fake application

If the code you want to test depends of a running application, you can easily create a `FakeApplication` on the fly:

```
@Test
public void findById() {
    running(fakeApplication(), new Runnable() {
       public void run() {
           Computer macintosh = Computer.find.byId(21l);
           assertThat(macintosh.name).isEqualTo("Macintosh");
           assertThat(formatted(macintosh.introduced)).isEqualTo("1984-01-24");
       }
    });
}
```

You can also pass (or override) additional configuration to the fake application, or mock any plugin. For example to create a `FakeApplication` using a `default` in memory database:

```
fakeApplication(inMemoryDatabase())
```

> **Next:** [[Writing functional tests | JavaFunctionalTest]]