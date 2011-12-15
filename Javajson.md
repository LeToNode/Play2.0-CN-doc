# JSON support

Play includes the [Jackson](http://jackson.codehaus.org/) library as a dependency _(A good tutorial of Jackson can be found [here](http://wiki.fasterxml.com/JacksonInFiveMinutes))_. What this means in practice is that one can serialize from and to JSON format. 

# render JSON
As for rendering JSON, there is a helper method ```play.json.Render.toJson``` that can be used to send JSON as a response. For example:

```
import play.*;
import play.mvc.*;
import static play.json.Render.toJson;

public class MyController extends Controller {
  
  public static Result index() {
    Map<String,String> d = new HashMap<String,String>();
    d.put("peter","foo");
    d.put("yay","value");
    return ok(toJson(d));
   }   
}
```

