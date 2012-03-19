# HTTP 路由 (Routing)

## 内置的 HTTP 路由(HTTP Router)

路由是一种把每一个HTTP请求转换成一个Action(控制器Controller里的一个静态函数)的组件.

HTTP请求可以认为是MVC框架中的一个事件(Event). 这个事件主要包含两方面的内容:

- 请求路径 (如 `/clients/1542`, `/photos/list`), 包括查询请求串.
- HTTP请求类型 (GET, POST, ...).

路由配置在 `conf/routes` 文件内. 你可以再浏览器中直接看到这样的路由错误:

[[images/routesError.png]]

## 路由文件语法

`conf/routes` 文件是路由使用的配置文件.它列出了所以当前应用用到的路径. 每一个路由条目包含一个请求类型、请求路径和处理请求的Action.

路由可以这样设置:

```
GET   /clients/:id          controllers.Clients.show(id: Long)  
```

> 注意Action里的内容, 参数类型紧跟在参数名称后面, 这点和Scala一样.

每一个路由由一个HTTP类型开头，紧跟请求路径的模式. 最后一部分是具体哪个Action去处理请求.

同样，你可以使用 `#` 符号在路由里写注释:

```
# Display a client.
GET   /clients/:id          controllers.Clients.show(id: Long)  
```

## HTTP请求类型

这里的HTTP请求类型可以是任何HTTP标准请求类型 (`GET`, `POST`, `PUT`, `DELETE`, `HEAD`).

## URI模式(请求路径)

URI模式定义了请求的路径模式. 这其中的的某一部分可以使动态的.

### 静态路径

比如, 完全匹配 `GET /clients/all` 请求, 你可以这么定义路由:

```
GET   /clients              controllers.Clients.list()
```

### 动态路径 

如果你想要处理请求的时候接受一部分动态内容，比如id，你需要定义一个动态路由:

```
GET   /clients/:id          controllers.Clients.show(id: Long)  
```

> 注意，一个路由可能含有多个动态部分.

对于动态部分的默认匹配表达式规则为：`[^/]+`，这意味着像`:id`这样的动态部分定义将会精确的匹配一个URI路径片段


### Dynamic parts spanning several /
### 动态匹配多个路径 /


If you want a dynamic part to capture more than one URI path segment, separated by forward slashes, you can define a dynamic part using the `*id` syntax, which uses the `.*` regular expression:

如果你想一个动态部分捕获多个URI路径片段，在前面加一个斜扛分割，你可以使用`*id`语法来定义一个动态部分，这种语法是使用了正则表达式中的：`.*`


```
GET   /files/*name          controllers.Application.download(name)  
```

Here, for a request like `GET /files/images/logo.png`, the `name` dynamic part will capture the `images/logo.png` value.

现在，对于像`GET /files/images/logo.png`这样的请求，动态部分的`name`将会捕获`images/logo.png`的值


### Dynamic parts with custom regular expressions

使用用户自定义的正则表达式来定义动态部分


You can also define your own regular expression for a dynamic part, using the `$id<regex>` syntax:

你也可以定义你自己的正则表达式来定义动态部分，使用`$id<regex>`语法

```
GET   /clients/$id<[0-9]+>  controllers.Clients.show(id: Long)  
```

## Call to action generator method
## 调用action的方法


The last part of a route definition is the call. This part must define a valid call to an action method.
If the method does not define any parameters, just give the fully-qualified method name:

路由定义的最后一部分就是调用。这部分必须给一个action的方法定义一个有效的调用
如果方法没有定义任何参数，只需要给出完整的方法名：

```
GET   /                     controllers.Application.homePage()
```

If the action method defines parameters, the corresponding parameter values will be searched for in the request URI, either extracted from the URI path itself, or from the query string.

如果action方法定义了参数，相应的参数值将会在请求的URI，或者从URI路径本身和查询字符串中获取

```
# Extract the page parameter from the path.
# 从路径中获取页面参数
# i.e. http://myserver.com/index
GET   /:page                controllers.Application.show(page)
```

或者:

```
# 从查询字符串中获取页面参数.
# i.e. http://myserver.com/?page=index
GET   /                     controllers.Application.show(page)
```

这是在 `controllers.Application` controller中定义的`show`方法：
```java
public static Result show(String page) {
  String content = Page.getContentOf(page);
  response().setContentType("text/html");
  return ok(content);
}
```

### 参数类型

对于`String`类型的参数，参数的类型是可选的。如果你像play正确的转换成对应的Scala类型，你可以添加一个明确的类型：

```
GET   /client/:id           controllers.Clients.show(id: Long)
```

然后在controller里相应的action方法中用相同类型的参数：

```java
public static Result show(Long id) {
  Client client = Client.findById(id);
  return ok(views.html.Client.show(client));
}
```

> **Note:** The parameter types are specified using a suffix syntax. Also The generic types are specified using the `[]` symbols instead of `<>`, as in Java. For example, `List[String]` is the same type as the Java `List<String>`.

### Parameters with fixed values

Sometimes you’ll want to use a fixed value for a parameter:

```
# Extract the page parameter from the path, or fix the value for /
GET   /                     controllers.Application.show(page = "home")
GET   /:page                controllers.Application.show(page)
```

### Parameters with default values

You can also provide a default value that will be used if no value is found in the incoming request:

```
# Pagination links, like /clients?page=3
GET   /clients              controllers.Clients.list(page: Integer ?= 1)
```

## Routing priority

Many routes can match the same request. If there is a conflict, the first route (in declaration order) is used.

## Reverse routing

The router can be used to generate a URL from within a Java call. This makes it possible to centralize all your URI patterns in a single configuration file, so you can be more confident when refactoring your application.

For each controller used in the routes file, the router will generate a ‘reverse controller’ in the `routes` package, having the same action methods, with the same signature, but returning a `play.mvc.Call` instead of a `play.mvc.Result`. 

The `play.mvc.Call` defines an HTTP call, and provides both the HTTP method and the URI.

For example, if you create a controller like:

```java
package controllers;

import play.*;
import play.mvc.*;

public class Application extends Controller {
    
  public static Result hello(String name) {
      return ok("Hello " + name + "!");
  }
    
}
```

And if you map it in the `conf/routes` file:

```
# Hello action
GET   /hello/:name          controllers.Application.hello(name)
```

You can then reverse the URL to the `hello` action method, by using the `controllers.routes.Application` reverse controller:

```java
// Redirect to /hello/Bob
public static Result index() {
    return redirect(controllers.routes.Application.hello("Bob")); 
}
```

> **Next:** [[Manipulating the response | JavaResponse]]