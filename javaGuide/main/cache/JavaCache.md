# Play Cache

使用缓存是现在应用程序的一个典型优化措施，因此Play提供了一个全局cache. 需要注意到的一点是，这里的缓存就像其他任何缓存一样：你刚刚存储的数据也可能会马上丢失.

对于任何存储于cache中的数据来说，都需要一种重建策略以防止数据丢失.这种处事风格也正式Play不同于Java EE的一个方面.JavaEE 会期望session在整个生命周期内都是有效的.

Play默认的cache实现使用了[[EHCache| http://ehcache.org/]].你也可以通过plugin的方式提供自己的实现.

## 访问 Cache API

Cache 使用的是 `play.cache.Cache` 对象. 它需要预先注册一个cache plugin.

> **Note:** 这个API为了最大先读的支持plugin实现，故意减少了功能.如果你需要更多的操作，可以通过提供自己的cache plugin来实现.

使用这个简单的API你就可以在cache中存储数据了:

```
Cache.set("item.key", frontPageNews);
```

然后从cache中提取数据:

```
News news = Cache.get("item.key");
```

## 缓存 HTTP 响应

You can easily create a smart cached action using standard `Action` composition. 

> **Note:** Play HTTP `Result` instances are safe to cache and reuse later.

Play provides a default built-in helper for the standard case:

```
@Cached("homePage")
public static Result index() {
  return ok("Hello world");
}
```

## Caching in templates

You may also access the cache from a view template.

```
@cache.Cache.getOrElse("cached-content", 3600) {
     <div>I’m cached for an hour</div>
}
```

## Session cache

Play provides a global cache, whose data are visible to anybody. How would one restrict visibility to a given user? For instance you may want to cache metrics that only apply to a given user.


```
// Generate a unique ID
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


> **Next:** [[Calling web services | JavaWS]]