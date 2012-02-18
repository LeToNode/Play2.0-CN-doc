# The Play cache API

The default implementation of the Cache api use [[EHCache| http://ehcache.org/]]. You can also provide your own implementation via a plugin.

## Accessing the Cache API

The cache api is provided by the `play.cache.Cache` object. It requires to have at least a cache plugin registered.

> **Note:** The API is voluntary minimal to allow several implementation to be plugged. If you need a more specific API, use the one provided by your Cache plugin.

Using this simple API you can either store data in cache:

```
Cache.set("item.key", connectedUser)
```

And then retrive it later:

```
User maybeUser = Cache.get("item.key")
```

## Caching HTTP responses

You can easily create smart cached action using standard `Action` composition. 

> **Note:** Play HTTP `Result` are safe to cache and reuse later.

Play provide a default built-in helper for standard cases:

```
@Cached("homePage")
public static Result index() {
  return ok("Hello world");
}
```

> **Next:** [[Calling WebServices | JavaWS]]
