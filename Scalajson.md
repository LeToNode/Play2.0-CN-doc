# Working with JSON

The recommend way of dealing with JSON in a play app is using Play's Json library. Play's JSON library was inspired by SJSON's Typeclass based JSON serialization but it was in fact built on top of [Jerkson](https://github.com/codahale/jerkson/).

# How to deserialize form JSON 

# How to serialize to JSON

# Other options

Just like in SJSON, if the typeclass based solution is not desirable for some reason, one can always fall back to a reflection based solution. [Jerkson](https://github.com/codahale/jerkson/) is a solid reflection based library and it's bundled with Play. 

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