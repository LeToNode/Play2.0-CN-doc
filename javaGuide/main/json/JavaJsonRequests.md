# Handling and serving Json requests

## Handling a Json request

A Json request is an HTTP request using a valid Json payload as request body. It must specify the `text/json` or `application/json` mime type in its `Content-Type` header.

By default an action uses a **any content** body parser. Then you can ask to retrieve the body as Json (actually as a jerkson `JsonNode`):

```
public static index sayHello() {
  JsonNode json = request().body().asJson();
  if(json == null) {
    return badRequest("Expecting Json data");
  } else {
    String name = json.findPath("name").getTextValue();
    if(name == null) {
      return badRequest("Missing parameter [name]");
    } else {
      return ok("Hello " + name);
    }
  }
}
```

Of course it's way better (and simpler) to specify our own `BodyParser` to ask Play to parser the content body directly as Json:

```
@BodyParser.Of(Json.class)
public static index sayHello() {
  String name = json.findPath("name").getTextValue();
  if(name == null) {
    return badRequest("Missing parameter [name]");
  } else {
    return ok("Hello " + name);
  }
}
```

> **Note:** This way a 400 HTTP response will be automatically returned for non json request. 

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

In our previous example we handle a Json request, but we reply with a `text/plain` response. Let'as change that to send back a valid Json HTTP response:

```
@BodyParser.Of(Json.class)
public static index sayHello() {
  ObjectNode result = Json.newObject();
  String name = json.findPath("name").getTextValue();
  if(name == null) {
    result.put("status", "KO");
    result.put("message", "Missing parameter [name]");
    return badRequest(result);
  } else {
    result.put("status", "OK");
    result.put("message", "Hello " + name);
    return ok(result);
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

> **Next:** [[Working with XML | JavaXmlRequests]]