# Session 和 Flash 作用域

## Play中有什么不同

如果你需要在多个HTTP请求的时候公用一些数据, 你就可以把数据存放在Session或Flash中. 存储在Session中的数据在整个用户Session周期内都有效, Flash中存储的数据仅仅在当前用户下次请求的时候有效.

session和flash的数据不是存储在服务器上，而是存储在Cookie中，这就意味着数据的大小将受到极大的限制(最大4K)并且只能存储string类型。

Cookies 有一个加密的签名，所以，客户端不能修改cookie 数据(或者使它失效)。play中的session不能作为缓存使用，如果你需要缓存关联数据到指定的session，你可以使用play内置的缓存机制，通过在用户的session中存储一个唯一ID来关联指定用户的缓存数据。


> There is no technical timeout for the session, which expires when the user closes the web browser. If you need a functional timeout for a specific application, just store a timestamp into the user Session and use it however your application needs (e.g. for a maximum session duration, maxmimum inactivity duration, etc.).

## 读取session中的数据

You can retrieve the incoming Session from the HTTP request:

```
public static Result index() {
  String user = session("connected");
  if(user != null) {
    return ok("Hello " + user);
  } else {
    return unauthorized("Oops, you are not connected");
  }
}
```

## 向session中存储数据

As the Session is just a Cookie, it is also just an HTTP header, but Play provides a helper method to store a session value:

```
public static Result index() {
  session("connected", "user@gmail.com");
  return ok("Welcome!");
}
```

The same way, you can remove any value from the incoming session:

```
public static Result index() {
  session.remove("connected");
  return ok("Bye");
}
```

## 清除全局session

If you want to discard the whole session, there is special operation:

```
public static Result index() {
  session().clear();
  return ok("Bye");
}
```

## Flash 作用域

The Flash scope works exactly like the Session, but with two differences:

- data are kept for only one request
- the Flash cookie is not signed, making it possible for the user to modify it.

> **Important:** The flash scope should only be used to transport success/error messages on simple non-Ajax applications. As the data are just kept for the next request and because there are no guarantees to ensure the request order in a complex Web application, the Flash scope is subject to race conditions.

Here are a few examples using the Flash scope:

```
public static Result index() {
  String message = flash("success");
  if(message == null) {
    message = "Welcome!";
  }
  return ok(message);
}

public static Result save() {
  flash("success", "The item has been created");
  return redirect("/home");
}
```

> **Next:** [[Body parsers | JavaBodyParsers]]