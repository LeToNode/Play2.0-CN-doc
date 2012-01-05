# Working with JSON

The recommend way of dealing with JSON in a play app is using Play's JSON library. Play's JSON library was inspired by [SJSON](https://github.com/debasishg/sjson)'s Typeclass based JSON serialization but in fact it was built on top of [Jerkson](https://github.com/codahale/jerkson/) (which in return a Scala wrapper around the fast Java based JSON library, [Jackson](http://jackson.codehaus.org/)). The benefit of this approach is that both the java and the scala side of Play can share the same underlying library (Jackson), while we could avoid reimplementing many serialization facilities that Jerkson can provide.

# How to deserialize form JSON 

# How to serialize to JSON

# Other options

Just like with [SJSON](https://github.com/debasishg/sjson), a reflection based solution is also available if the typeclass based implemention is not desirable. [Jerkson](https://github.com/codahale/jerkson/) is a solid reflection based JSON library and it's bundled with Play. 

Here is a small snippet which shows to serialize plain scala objects into JSON and send it over the wire:
```scala
object MyController extends Controller{

 import com.codahale.jerkson.Json._

 def sendJson(id: String) = Action {
    val dataFromDataStore =  Map("url"->"http://nytimes.com","attributes"-> Map("name"->"nytimes", "country"->"US","id"->25), "links"->List("http://link1","http://link2")
    Ok(generate(dataFromDataStore)).as("application/json")
  }

}
```