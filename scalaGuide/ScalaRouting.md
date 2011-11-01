# Http Routing

The router is the component in charge of translating incoming HTTP Requests into action calls (a static, public method of a Controller).

An HTTP request is seen as an event by the MVC framework. The event contains two major pieces of information:

- The Request path (such as `/clients/1542`, `/photos/list`), including the query string.
- The HTTP method (GET, POST, PUT, DELETE)

## The routes file syntax

The `conf/routes` file is the configuration file used by the Router. This file lists all the routes needed by the application. Each route consists of an HTTP method + URI pattern associated with a call to an `Action` generator.

Let’s see what a route definition looks like:

```python
GET    /clients/:id             Clients.show(id: Long)  
```

Each route starts with the HTTP method, followed by the URI pattern. The last element of a route is the call definition.

You can add a comment to the route file, with the `#` character.

```python
# Display a client
GET    /clients/:id             Clients.show(id: Long)  
```

### The HTTP method

The HTTP method can be any of the valid methods supported by HTTP (`GET`,`POST`,`PUT`,`DELETE`,`HEAD`).

### The URI Pattern

The URI pattern defines the route’s request path. Some parts of the request path can be dynamic.

For example, to exactly match the `GET /clients/all` incoming requests, you can define this route:

```python
GET    /clients/all             Clients.list()
```

But if you want to define a route that retrieve a client by id, you need to add a dynamic part:

```python
GET    /clients/:id             Clients.show(id: Long)  
```

A URI pattern may have more than one dynamic part:

```python
GET    /clients/:id/accounts/:accountId    Clients.show(id: Long, accountId: Long)  
```
