# Handling and serving Json requests

## Handling a Xml request

A Xml request is an HTTP request using a valid Xml payload as request body. It must specify the `text/xml` mime type in its `Content-Type` header.

By default an action uses a **any content** body parser. Then you can ask to retrieve the body as Xml (actually as a `org.w3c.Document`):

```
public static index sayHello() {
  Document dom = request().body().asXml();
  if(dom == null) {
    return badRequest("Expecting Xml data");
  } else {
    String name = XPath.selectText("//name", dom);
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
@BodyParser.Of(Xml.class)
public static index sayHello() {
  String name = XPath.selectText("//name", dom);
  if(name == null) {
    return badRequest("Missing parameter [name]");
  } else {
    return ok("Hello " + name);
  }
}
```

> **Note:** This way a 400 HTTP response will be automatically returned for non xml request. 

You can test it with **cUrl** from a command line:

```
curl 
  --header "Content-type: text/xml" 
  --request POST 
  --data '<name>Guillaume</name>' 
  http://localhost:9000/sayHello
```

It replies with:

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Content-Length: 15

Hello Guillaume
```

## Serving a Xml response

In our previous example, we handle an XML request, but we reply with a `text/xml` response. Let's change it and send back a valid Xml HTTP response:

```
@BodyParser.Of(Xml.class)
public static index sayHello() {
  String name = XPath.selectText("//name", dom);
  if(name == null) {
    return badRequest("<message \"status\"=\"KO\">Missing parameter [name]</message>");
  } else {
    return ok("<message \"status\"=\"OK\">Hello " + name + "</message>");
  }
}
```

Now it replies with:

```
HTTP/1.1 200 OK
Content-Type: text/xml; charset=utf-8
Content-Length: 46

<message status="OK">Hello Guillaume</message>
```

> **Next:** [[Handling file upload | JavaFileUpload]]