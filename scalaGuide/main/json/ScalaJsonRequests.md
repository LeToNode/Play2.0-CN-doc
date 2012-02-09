# Handling and serving Json requests

## Handling a Json request

A Json request is an HTTP request using a valid Json payload as request body. It must specify the `text/json` or `application/json` mime type in its `Content-Type` header.

By default an `Action` uses a **any content** body parser. Then you can ask to retrieve the body as Json (actually as a `JsValue`):

```
def sayHello = Action { request =>
  request.body.asJson.map { json =>
    (json \ "name").asOpt[String].map { name =>
      Ok("Hello " + name)
    }.getOrElse {
      BadRequest("Missing parameter [name]")
    }
  }.getOrElse {
    BadRequest("Expecting Json data")
  }
}
```

Of course it's way better (and simpler) to specify our own `BodyParser` to ask Play to parser the content body directly as Json:

```
def sayHello = Action(parse.json) { request =>
  (request.body \ "name").asOpt[String].map { name =>
    Ok("Hello " + name)
  }.getOrElse {
    BadRequest("Missing parameter [name]")
  }
}
```

> **Note:** When using a json body parser, the `request.body` value is directly a valid `JsValue`. 

You can test it with **cUrl** from a command line:

```
curl 
  --header "Content-type: application/json" 
  --request POST 
  --data '{"name": "Guillaume"}' 
  http://localhost:9000/sayHello
```

It replies with:

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Content-Length: 15

Hello Guillaume
```

## Serving a Json response

In our previous example we handle a Json request, but we reply with a `text/plain` response. Let's change that to send back a valid Json HTTP response:

```
def sayHello = Action(parse.json) { request =>
  (request.body \ "name").asOpt[String].map { name =>
    Ok(toJson(
      Map("status" -> "OK", "message" -> ("Hello " + name))
    ))
  }.getOrElse {
    BadRequest(toJson(
      Map("status" -> "KO", "message" -> "Missing parameter [name]")
    ))
  }
}
```

Now it replies with:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 43

{"status":"OK","message":"Hello Guillaume"}
```

> **Next:** [[Working with XML | ScalaXmlRequests]]