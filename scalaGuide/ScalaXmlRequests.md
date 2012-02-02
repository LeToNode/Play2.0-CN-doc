# Handling and serving Xml requests

## Handling an Xml request

An Xml request is an HTTP request using a valid Xml payload as request body. It must specify the `text/xml` mime type in its `Content-Type` header.

By default an `Action` uses a **any content** body parser. Then you can ask to retrieve the body as Xml (actually as a `NodeSeq`):

```
def sayHello = Action { request =>
  request.body.asXml.map { xml =>
    (xml \\ "name" headOption).map(_.text).map { name =>
      Ok("Hello " + name)
    }.getOrElse {
      BadRequest("Missing parameter [name]")
    }
  }.getOrElse {
    BadRequest("Expecting Xml data")
  }
}
```

Of course it's way better (and simpler) to specify our own `BodyParser` to ask Play to parser the content body directly as Xml:

```
def sayHello = Action(parse.xml) { request =>
  (request.body \\ "name" headOption).map(_.text).map { name =>
    Ok("Hello " + name)
  }.getOrElse {
    BadRequest("Missing parameter [name]")
  }
}
```

> **Note:** When using a xml body parser, the `request.body` value is directly a valid `NodeSeq`. 

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

## Serving an Xml response

In our previous example we handle an Xml request, but we reply with a `text/plain` response. Let'as change that to send back a valid Xml HTTP response:

```
def sayHello = Action(parse.xml) { request =>
  (request.body \\ "name" headOption).map(_.text).map { name =>
    Ok(<message status="OK">Hello {name}</message>)
  }.getOrElse {
    BadRequest(<message status="KO">Missing parameter [name]</message>)
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

> **Next:** [[Handling file upload | ScalaFileUpload]]