# Session and Flash scopes

## How it is different in Play

If you have to keep data across multiple HTTP Requests, you can save them in the Session or the Flash scope. Data stored in the Session are available during the whole user session, and data stored in the flash scope are available to the next request only.

It’s important to understand that Session and Flash data are not stored in the server but are added to each subsequent HTTP Request, using the Cookie mechanism. So the data size is very limited (up to 4 KB) and you can only store String values.

Of course, cookies are signed with a secret key so the client can’t modify the cookie data (or it will be invalidated). The Play session is not aimed to be used as a cache. If you need to cache some data related to a specific session, you can use the Play built-in cache mechanism and use store a unique id in the user session to keep them related to a specific user.

> There is no technical timeout for the session. It expires when the user close its web browser. If you need a functional timeout for a specific application, just store a timestamp into the user session and check it along your application needs (max session duration, max inactivity duration, etc.).

## Reading a Session value

You can retrieve the incoming Session from the HTTP request:

```
def index = Action { request =>
  request.session.get("connected").map { user =>
    Ok("Hello " + user)
  }.getOrElse {
    Unauthorized("Oops, you are not connected")
  }
}
```

Alternatively you can retrieve the Session implicitely from a request:

```
def index = Action { implicit request =>
  session.get("connected").map { user =>
    Ok("Hello " + user)
  }.getOrElse {
    Unauthaurized("Oops, you are not connected")
  }
}
```

## Storing data into the Session

As the Session is just a Cookie, it is also just an HTTP header. You can manipulate the session data the same way you are manipulating other results properties:

```
Ok("Welcome!").withSession(
  "connected" -> "user@gmail.com"
)
```

Note that it will replace the whole session. If you need to add an element to an existing Session, just add an element to the incoming session, and specify that as new session:

```
Ok("Hello World!").withSession(
  session ++ ("saidHello" -> "yes")
)
```

The same way, you can remove any value from the incoming session:

```
Ok("Theme reset!").withSession(
  session - "theme"
)
```

## Discarding the whole session

If you want to discard the whole session, there is special operation:

```
Ok("Bye").withNewSession
```

## Flash scope

The flash scope works exactly like the session, but:

- Data are kept for only one request.
- The flash cookie is not signed, making it possible for the user to modify it.

> **Important:** Anyway the flash scope should only be used to transport success/error messages on simple non-Ajax applications. As the data are just kept for the next request and as there are no guarantees to ensure the requests order in a complex Web application, the flash scope is subject to race conditions.

Here are a few examples using the flash scope:

```
def index = Action { implicit request =>
  Ok {
    flash.get("success").getOrElse("Welcome!")
  }
}

def save = Action {
  Redirect("/home").flashing(
    "success" -> "The item has been created"
  )
}
```

> **Next:** [[Body parsers | ScalaBodyParsers]]