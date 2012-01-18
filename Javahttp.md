# Making HTTP Requests

Sometimes we would like to call other HTTP services from within a play application. Play supports this task via its ```play.libs.WS``` library which provides a way to make asynchronous HTTP calls. 

Any calls made by ```play.libs.WS``` should return a ``` play.libs.F.Promise<play.libs.WS.Response>``` which we can later handle with play's asynchronous mechanisms.

# A short introduction

## Hello Get
```java
import play.libs.WS;

play.libs.F.Promise<play.libs.WS.Response> promise = WS.url("http://mysite.com").get();

play.libs.WS.Response res = promise.get();

String body = res.getBody();

int status = res.getStatus();

String contentType = res.getHeader("Content-Type");

org.w3c.dom.Document doc = res.asXml();

org.codehaus.jackson.JsonNode json = res.asJson();

```
_Note: in this example we used  ```play.libs.F.Promise#get``` to retrieve the result of the promise in a blocking fashion, however, since this is an async call, one might want to avoid blocking by using ```play.libs.F.Promise#onRedeem``` callback or 
```flatMap``` and ```map```. Please consult the javadoc for more [information](https://github.com/playframework/Play20/blob/master/framework/src/play/src/main/java/play/libs/F.java)_

## Hello Post
```java

```

one also can add custom request and authentication headers and even a signature. For more information please see the api doc [here](https://github.com/playframework/Play20/blob/master/framework/src/play/src/main/java/play/libs/WS.java)
