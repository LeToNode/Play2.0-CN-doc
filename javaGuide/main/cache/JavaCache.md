# The Play cache API

Caching data is a typical optimization in moderner applications. Play provides a global Cache facility for that purpose. An important point about the cache, is that it behaves just as a real cache: The data you just stored may just go missing.

For any data stored in the cache, a regeneration strategy needs to be put in place should the data go missing. This phylosophy is one of the fundamentals behind Play! and is typically different from J2EE where the session is expected to hold the value during its lifetime. 

The default implementation of the Cache api uses [[EHCache| http://ehcache.org/]]. You can also provide your own implementation via a plugin.

## Accessing the Cache API

The cache api is provided by the `play.cache.Cache` object. It requires to have at least a cache plugin registered.

> **Note:** The API is voluntary minimal to allow several implementations to be plugged. If you need a more specific API, use the one provided by your Cache plugin.

Using this simple API you can either store data in cache:

```
Cache.set("item.key", frontPageNews);
```

And then retrive it later:

```
News news = Cache.get("item.key");
```

## Caching HTTP responses

You can easily create a smart cached action using standard `Action` composition. 

> **Note:** Play HTTP `Result` are safe to cache and reuse later.

Play provides a default built-in helper for standard cases:

```
@Cached("homePage")
public static Result index() {
  return ok("Hello world");
}
```

## Caching in Templates

You may also access the cache from a View.

```
@cache.Cache.getOrElse("cached-content", 3600) {
  <div>I’m cached for an hour</div>
}
```

## Session Cache

Play provides a global cache facily, the data stored is visible to anybody. How would one restrict visibility to a given user? For instance you may want to cache metrics that only apply to a given user.


```
// Generate a unique id
String uuid=session("uuid");
if(uuid==null) {
	uuid=java.util.UUID.randomUUID().toString();
	session("uuid", uuid);
}

// Access the cache
News userNews = Cache.get(uuid+"item.key");
if(userNews==null) {
	userNews = generateNews(uuid);
	Cache.set(uuid+"item.key", userNews );
}
```


> **Next:** [[Calling WebServices | JavaWS]]
