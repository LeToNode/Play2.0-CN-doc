# Http Routing

The router is the component in charge of translating incoming HTTP Requests into action calls (a static, public method of a Controller).

An HTTP request is seen as an event by the MVC framework. The event contains two major pieces of information:

- The Request path (such as `/clients/1542`, `/photos/list`), including the query string.
- The HTTP method (GET, POST, ...)

## The routes file syntax

The `conf/routes` file is the configuration file used by the Router. This file lists all the routes needed by the application. Each route consists of an HTTP method + URI pattern associated with a call to an `Action` generator.

Let’s see what a route definition looks like:

```
GET   /clients/:id          controllers.Clients.show(id: Long)  
```

Each route starts with the HTTP method, followed by the URI pattern. The last element of a route is the call definition.

You can add a comment to the route file, with the `#` character.

```
# Display a client
GET   /clients/:id          controllers.Clients.show(id: Long)  
```

### The HTTP method

The HTTP method can be any of the valid methods supported by HTTP (`GET`, `POST`, `PUT`, `DELETE`, `HEAD`).

### The URI Pattern

The URI pattern defines the route’s request path. Some parts of the request path can be dynamic.

For example, to exactly match the `GET /clients/all` incoming requests, you can define this route:

```
GET   /clients              controllers.Clients.list()
```

But if you want to define a route that retrieve a client by id, you need to add a dynamic part:

```
GET   /clients/:id          controllers.Clients.show(id: Long)  
```

> Note that a URI pattern may have more than one dynamic part.

The default matching strategy for a dynamic part is defined by the regular expression `[^/]+`, meaning that any dynamic part defined as `:id` will match exactly one URI part.

If you want to capture more than URI part, you can define a dynamic part using the `*id` syntax, that will use the `.*` regular expression:

```
GET   /files/*name          controllers.Application.download(name)  
```

Here for a request like `GET /files/images/logo.png`, the `name` dynamic part will capture the `images/logo.png` value.

You can also defines your own regular expression for a dynamic part, using the `$id<regex>` syntax:
    
```
GET   /clients/$id<[0-9]+>  controllers.Clients.show(id: Long)  
```

To summarize, you can define a dynamic part using one of these 3 forms:

- `:id`
- `*id`
- `$id<[0-9]+>`

### Call to action generator method.

The last part of a route definition is the call. This part must define a valid call to a method returning a `play.api.mvc.Action` value, so typically to a Controller action method.

If the method does not define any parameter, just define the fully qualified method name:

```
GET   /                     controllers.Application.homePage()
```

If the action method defines some parameters, all these parameter values will be searched in the URI part, either extracted from the URI path itself, or from the QueryString.

```
# Extract the page parameter from the path
GET   /:page                controllers.Application.show(page)
```

Or:

```
# Extract the page parameter from the queryString
GET   /page                 controllers.Application.show(page)
```

And the corresponding, `show` method definition in the `controllers.Application` controller:

```scala
def show(page: String) = Action {
    contentOf(page).map { htmlContent =>
        Ok(htmlContent).as("text/html")
    }.getOrElse(NotFound)
}
```

For parameter of type `String`, typing the parameter is optional. But if you want that Play transform the incoming parameter into a specific scala type, you must explicitely type the parameter:

```
GET   /client/:id           controllers.Clients.show(id: Long)
```

And the corresponding, `show` method definition in the `controllers.Clients` controller:

```scala
def show(id: Long) = Action {
    Client.findById(id).map { client =>
        Ok(views.html.Clients.display(client))
    }.getOrElse(NotFound)
}
```

Sometimes you want to use a fixed value for a parameter:

```
# Extract the page parameter from the path, or fix the value for /
GET   /                     controllers.Application.show(page = "home")
GET   /:page                controllers.Application.show(page)
```

You can also provide a default value, meaning that if no value is found is the incoming request, the default value will be used:

```
# Pagination links, like /clients?page=3
GET   /clients              controllers.Clients.list(page: Int ?= 1)
```
