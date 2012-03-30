# Session 和 Flash 作用域

## Play中有什么不同

如果你需要在多个HTTP请求的时候公用一些数据, 你就可以把数据存放在Session或Flash中. 存储在Session中的数据在整个用户Session周期内都有效, Flash中存储的数据仅仅在当前用户下次请求的时候有效.

session和flash的数据不是存储在服务器上，而是存储在Cookie中，这就意味着数据的大小将受到极大的限制(最大4K)并且只能存储string类型。

Cookies 有一个加密的签名，所以，客户端不能修改cookie 数据(或者使它失效)。play中的session不能作为缓存使用，如果你需要缓存关联数据到指定的session，你可以使用play内置的缓存机制，通过在用户的session中存储一个唯一ID来关联指定用户的缓存数据。


> session没有超时技术，当用户关闭浏览器的时候session失效。如果你的应用需要超时功能，只需要在用户session中存储一个时间戳无论你的应用是否需要(比如session持续的最长时间，最大的失效时间，等等).



## 读取session中的数据

你可以从HTTP 请求中来获得session：

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

同样的，你可以从session中移除数据：

```
public static Result index() {
  session.remove("connected");
  return ok("Bye");
}
```

## 清除全局session

如果你想清除整个session，可以这样操作：

```
public static Result index() {
  session().clear();
  return ok("Bye");
}
```

## Flash 作用域

Flash的范围正像session一样，但是有两点不同：


- 数据只存在一次请求中
- Flash cookie没有签名，用户可以修改它

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