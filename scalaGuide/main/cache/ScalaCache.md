# The Play cache API

The default implementation of the Cache api use [[EHCache| http://ehcache.org/]]. You can also provide your own implementation via a plugin.

## Accessing the Cache API

The cache api is provided by the `play.api.cache.Cache` object. It requires to have at least a cache plugin registered.

> **Note:** The API is voluntary minimal to allow several implementation tu be plugged. If you need a more specific API, use the one provided by your Cache plugin.

Using this simple API you can either store data in cache:

```
Cache.set("item.key", connectedUser)
```

And then retrive it later:

```
val maybeUser: Option[User] = Cache.getAs[User]("item.key")
```

There is also a convenient helper to retrieve from cache or set the value in cache if it was missing:

```
val user: User = Cache.getOrElseAs[User]("item.key") {
  User.findById(connectedUser)
}
```

## Caching HTTP responses

You can easily create smart cached action using standard Action composition. 

> **Note:** Play HTTP `Result` are safe to cache and reuse later.

Play provide a default built-in helper for standard cases:

```
def index = Cached("homePage") {
  Action {
    Ok("Hello world")
  }
}
```

Or even:

```
def userProfile = Authenticated { user =>
  Cached(req => "profile." + user) {      
    Action { 
      Ok(views.html.profile(User.find(user)))
    }   
  }
}
```

> **Next:** [[Calling WebServices | ScalaWS]]
