# 操作 HTTP 返回值 

## 改变默认的 Content-Type

结果的内容类型将自动指定为你在java中的值

比如:

```
Result textResult = ok("Hello World!");
```

将自动设置 `Content-Type` 的头为 `text/plain`, 当:

```
Result jsonResult = ok(jerksonObject);
```

将自动设置 `Content-Type` 的头为 `application/json`.

This is pretty useful, but sometimes you want to change it. Just use the `as(newContentType)` method on a result to create a new similiar result with a different `Content-Type` header:

这一点相当有用，但是你有时候需要改变他。只需要在结果集上创建一个有不同`Content-Type`头的相似的结果集的时候使用 `as(newContentType)` 方法

```
Result htmlResult = ok("<h1>Hello World!</h1>").as("text/html");
```

你也可以在HTTP响应中设置内容类型：

```
public static Result index() {
  response().setContentType("text/html");
  return ok("<h1>Hello World!</h1>");
}
```

## 设置 HTTP 响应头

你可以添加（或更新）任何HTTP响应头：

```
public static Result index() {
  response().setContentType("text/html");
  response().setHeader(CACHE_CONTROL, "max-age=3600");
  response().setHeader(ETAG, "xxx");
  return ok("<h1>Hello World!</h1>");
}
```

注意设置HTTP头的时候将会丢弃所有以前的值

## 设置和丢弃 cookies

Cookies 是一种特殊的HTTP头表单，但是play提供了简便的设置方法。


你可以非常简单的给HTTP响应添加一个Cookie:

```
response().setCookie("theme", "blue");
```

也可以丢弃一个以前存储在浏览器中的Cookie：

```
response().discardCookies("theme");
```

##  指定返回文本的字符编码

使用正确的字符编码对于HTTP响应中的文本是非常重要的。play默认的是使用`utf-8`。

编码通常用于将文本转换成响应的字节通过网络发送，和添加适当的`;charset=xxx`来扩展`Content-Type`头。

编码可以在你产生`Result`值的时候指定

```
public static Result index() {
  response().setContentType("text/html; charset=iso-8859-1");
  return ok("<h1>Hello World!</h1>", "iso-8859-1");
}
```

> **下一章:** [[Session 和 Flash 作用域 | JavaSessionFlash]]