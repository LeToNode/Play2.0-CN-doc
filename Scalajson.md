# Working with JSON

The recommend way of dealing with JSON in a play app is using Play's JSON library. Play's JSON library was inspired by [SJSON](https://github.com/debasishg/sjson)'s Typeclass based JSON serialization but in fact it was built on top of [Jerkson](https://github.com/codahale/jerkson/) (which in return a Scala wrapper around the fast Java based JSON library, [Jackson](http://jackson.codehaus.org/)). The benefit of this approach is that both the java and the scala side of Play can share the same underlying library (Jackson), while we could avoid reimplementing many serialization facilities that Jerkson can provide.

# How to deserialize from JSON 
 ```play.api.libs.json``` package contains 7 JSON data types: ```JsOBject```, ```JsNull```, ```JsUndefined```,```JsBoolean```, ```JsNumber```,```JsArray```, ```JsString```. All of them inherit from the generic JSON value, ```JsValue```.
Using these one can build a typesafe JSON serializer and encapsulate the whole logic like this

For example:
```scala
case class User(id: Long, name: String, friends: List[User])

  implicit object UserFormat extends Format[User] {
    def reads(json: JsValue): User = User(
      (json \ "id").as[Long],
      (json \ "name").as[String],
      (json \ "friends").asOpt[List[User]].getOrElse(List()))
    def writes(u: User): JsValue = JsObject(Nil) //unmarshalling to JSValue is covered bellow 
  }
```
given this, one can parse an incoming JSON to a User like this:  ```play.api.libs.json.parse(incomingJSONstring).as[User]```

it's also possible to pattern match on JsValue where the underlying JSON type is not homogenous (say a JSON Map with different JSON type values). 

```scala
 val data = Map("newspaper" -> Map("url"->"http://nytimes.com","attributes"-> Map("name"->"nytimes", "country"->"US","id"->25), "links"->List("http://link1","http://link2")))

case class Attributes(name: String, id: Int, links: List[String])

val attributes = (data \\ "attributes") 

println(Attributes( (attributes \ "name") match {case JsString(name)=>name;case _ => ""},
                    (attributes \ "id") match {case JsNumber(id)=>id;case _ => 0},
                    (attributes \ "links") match {case JsArray(links)=>links;case _ => Nil}))
   
```

 

# How to serialize to JSON
The benefit of the typeclass based solution that it's significantly increasing typesafety with the price of maintaing some extra Mapping or conversion which is necessary to unmarshall domain objects to JSON.


 
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