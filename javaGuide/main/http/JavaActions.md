# Action, Controller 和 Result

## 什么是 Action (动作)?

大多数的Play应用请求都是通过 `Action` 进行处理. 

Action基本上是一个处理请求参数的一个Java方法, 然后生成一个可以被发送到客户端的结果(Result).

```
public static Result index() {
  return ok("Got request " + request() + "!");
}
```

Action会返回一个 `play.mvc.Result` , 代表了一个要返回给网页客户端的 HTTP 结果. 在这个例子里 `ok` 构造了一个 **200 OK** 响应，并且包含了 **text/plain** 格式的返回内容.

## Controller (控制器)

控制器仅仅是一个继承了 `play.mvc.Controller`，并且把一些action方法组织在一起而已.

定义一个action的最简单方式就是使用一个没有参数的静态方法，返回一个 `Result` :

```
public static Result index() {
  return ok("It works!");
}
```

action同样可以有参数:

```
public static Result index(String name) {
  return ok("Hello" + name);
}
```

这些参数会被 `Router`（路由）解析，同时根据URI请求的参数值进行自动填充. 这些参数值可以通过URL或客户端请求串获得.

## Result 

我们可以从简单的Result开始: 一个包含HTTP返回状态、HTTP 头信息和body内容的Result.

Result由 `play.mvc.Result`定义, `play.mvc.Results`类提供了一些方法生成 HTTP 结果, 如上面用到的 `ok` 方法:

```
public static Result index() {
  return ok("Hello world!");
}
```

下面是一些创建不同类型Result的例子:

```
Result ok = ok("Hello world!");
Result notFound = notFound();
Result pageNotFound = notFound("<h1>Page not found</h1>").as("text/html");
Result badRequest = badRequest(views.html.form.render(formWithErrors));
Result oops = internalServerError("Oops");
Result anyStatus = status(488, "Strange response type");
```

在`play.mvc.Results` 类中可以找到所以这些Helper.

## Redirect(重定向)也是一种简单的Result

重定向也是一种简单的result形式，只不过它没有body内容。

创建重定向也有一些不同的方式:

```
public static Result index() {
  return redirect("/user/home");
}
```

默认是用一个 `303 SEE_OTHER` 返回类型, 你也可以制定一个更具体的跳转方式:

```
public static Result index() {
  return temporaryRedirect("/user/home");
}
```

> **Next:** [[HTTP 路由 | JavaRouting]]


