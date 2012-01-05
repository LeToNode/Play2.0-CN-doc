# Working with JSON

The recommend way of dealing with JSON in a play app is using Play's JSON library. 

Play's JSON library was inspired by [SJSON](https://github.com/debasishg/sjson)'s Typeclass based JSON serializer but actually it was built on top of [Jerkson](https://github.com/codahale/jerkson/) (which in return a Scala wrapper around the super-fast Java based JSON library, [Jackson](http://jackson.codehaus.org/)). 

The benefit of this approach is that both the java and the scala side of Play can share the same underlying library (Jackson), while we could avoid reimplementing many low level serialization facilities that Jerkson provides.

# How to parse JSON and marshal data to domain objects

 ```play.api.libs.json``` package contains 7 JSON data types: 

* ```JsOBject```
* ```JsNull```
* ```JsUndefined```
* ```JsBoolean```
* ```JsNumber```
* ```JsArray```
* ```JsString```

All of them inherit from the generic JSON value, ```JsValue```.

Using these one can build a typesafe JSON deserializer and encapsulate the whole logic like this

For example:

```scala
case class User(id: Long, name: String, friends: List[User])

  implicit object UserFormat extends Format[User] {
    def reads(json: JsValue): User = User(
      (json \ "id").as[Long],
      (json \ "name").as[String],
      (json \ "friends").asOpt[List[User]].getOrElse(List()))
    //unmarshaling to JSValue is covered in the next paragraph
    def writes(u: User): JsValue = JsUndefined("TODO")  

  }
```
_(note: Format defines two methods: ```reads``` and ```writes``` which are responsible for marshaling to and from JsValue.)_

given this, one can marshall an incoming JSON string into a User case class like this:  

```scala
val data = play.api.libs.json.parse(incomingJSONstring).as[User]
```

or if the data is coming from a request.body:

```scala
val user = Json.parse(request.body.asText.get).as[User]
```


it's also possible to pattern match on ```JsValue``` in cases where the underlying JSON type is not homogenous (say a JSON Map with different JSON types) or if one wants to manage the object creation.

```scala
import play.api.libs.json._

 val data = Map("newspaper" -> Map("url"->"http://nytimes.com","attributes"-> Map("name"->"nytimes", "country"->"US","id"->25), "links"->List("http://link1","http://link2")))

case class Attributes(name: String, id: Int, links: List[String])

val attributes = (data \\\ "attributes") 

println(Attributes( (attributes \ "name") match {case JsString(name)=>name;case _ => ""},
                    (attributes \ "id") match {case JsNumber(id)=>id;case _ => 0},
                    (attributes \ "links") match {case JsArray(links)=>links;case _ => Nil}))
   
```
_(note: \\\ means lookup in the current object and all descendants, \ means lookup corresponding property only)_

 

# How to unmarshal from domain objects to JSON

Of course, parsing JSON is just half of the story, since in most situations we would like to return JSON as well.

let's revisit the previous example:
```scala
import play.api.libs.json._

case class User(id: Long, name: String, friends: List[User])

  implicit object UserFormat extends Format[User] {
    def reads(json: JsValue): User = User(
      (json \ "id").as[Long],
      (json \ "name").as[String],
      (json \ "friends").asOpt[List[User]].getOrElse(List()))
    def writes(u: User): JsValue = JsObject(List(
      "id" -> JsNumber(u.id),
      "name" -> JsString(u.name),
      "friends" -> JsArray(u.friends.map(fr => JsObject(List("id" -> JsNumber(fr.id), "name" -> JsString(fr.name))))))) 
  }
```
_note: the main building block is JsObject which takes a Seq[String,JsValue]_

then

```scala
import play.api.libs.json._
import play.api._
import play.api.mvc._

object MyController extends Controller{

 def sendJson(id: String) = Action {
   val peter = User(id.toLong, "peter", friends: List())
    Ok(toJson(peter))
  }

}
```
or one can build an unnamed JsObject structure and return it:

```scala
import play.api.libs.json._
import play.api._
import play.api.mvc._

object MyController extends Controller{

 def sendJson(id: String) = Action {
    Ok(toJson(JsObject(List("key"->JsString("cool"), "links"->JsObject(List("name"->JsString("foo"), "links" ->JsArray(List(JsNumber(25)))))))))
  }

}
```

finally, it is also possible to unmarshal standard and typed datastructures directly:

```scala
import play.api.libs.json._
import play.api._
import play.api.mvc._

object MyController extends Controller{

 def sendJson(id: String) = Action {

    //this won't work, since it's not properly typed
    //toJson(Map("f"->Map[String,Any]("s"->List("1","f"),"f"->"f" )))
    
    //this will return JSON as a String
    //Json.stringify(toJson(Map("f"->Map("s"->List("1","f"))))) 
    
    //this will return a JSON string with content type "application/json"
    Ok(toJson(Map("f"->Map("s"->List("1","f")))))
  }

}
```

The benefit of the typeclass based solution that it's significantly increasing typesafety with the price of maintaing some extra Mapping or type conversion into JsValue which is necessary to unmarshal data from domain objects to JSON.


# Other options

while the typeclass based solution describe above is recommended, nothing stopping users from using any other JSON libraries.

For example, here is a small snippet which demonstrates how to marshal plain scala objects into JSON and send it over the wire using the bundled, reflection based [Jerkson](https://github.com/codahale/jerkson/ library:
```scala
object MyController extends Controller{

 import com.codahale.jerkson.Json._

 def sendJson(id: String) = Action {
    val dataFromDataStore =  Map("url"->"http://nytimes.com","attributes"-> Map("name"->"nytimes", "country"->"US","id"->25), "links"->List("http://link1","http://link2")
    Ok(generate(dataFromDataStore)).as("application/json")
  }

}
```