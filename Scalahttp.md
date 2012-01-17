# Making HTTP calls

Sometimes we would like to call other HTTP services from a play application. Play supports this task via its ```play.api.libs.WS``` library which provides a mechanism to make async http calls.

# A short introduction

## Hello Get
```scala
import play.api.libs._
val myfeed: Promise[ws.Response] = WS.url("http://mysite.com").get()
val content:String = myfeed.await(5000).get.body
val content:xml.Elem = myfeed.await(5000).get.xml
val content:JsValue = myfeed.await(5000).get.json
val code = myfeed.await(5000).get.status
```

## Hello Post
```scala
val content: String = await(WS.url("http://localhost:9001/post").post("content")).body
content must contain ("content")
content must contain("AnyContentAsText")

val contentForm: String = await(WS.url("http://localhost:9001/post").post(Map("param1"->Seq("foo")))).body
contentForm must contain ("AnyContentAsUrlFormEncoded")
contentForm must contain ("foo")
```

