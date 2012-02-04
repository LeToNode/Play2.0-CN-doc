# Play 2.0 for Scala developers

The Scala API for Play 2.0 application developers is available in the `play.api` package. 

> The API available directly inside the `play` package (such as `play.mvc`) is reserved for Java developers. As a Scala developer, look at `play.api.mvc`.

## Main concepts

- [[HTTP programming | ScalaActions]]
    - [[Actions, Controllers and Results | ScalaActions]]
    - [[HTTP routing | ScalaRouting]]
    - [[Manipulating results | ScalaResults]]
    - [[Session and Flash scopes | ScalaSessionFlash]]
    - [[Body parsers | ScalaBodyParsers]]
    - [[Actions composition | ScalaActionsComposition]]
- [[Asynchronous HTTP programming | ScalaAsync]]
    - [[Handling asynchronous results | ScalaAsync]]
    - [[Streaming HTTP responses | ScalaStream]]
    - [[Comet sockets | ScalaComet]]
    - [[WebSockets | ScalaWebSockets]]
- [[The template engine | ScalaTemplates]]
    - [[Scala templates syntax | ScalaTemplates]]
    - [[Common use cases | ScalaTemplateUseCases]]
- [[HTTP form submission and validation | ScalaForms]]
    - [[Form definitions | ScalaForms]]
    - [[Using the form template helpers | ScalaFormHelpers]]
- [[Working with Json | ScalaJson]]
    - [[The Play Json library | ScalaJson]]
    - [[Handling and serving Json requests | ScalaJsonRequests]]
- [[Working with XML | ScalaXmlRequests]]
    - [[Handling and serving XML requests | ScalaXmlRequests]]
- [[Handling file upload | ScalaFileUpload]]
    - [[Direct upload and multipart/form-data | ScalaFileUpload]]
- [[Accessing an SQL database | ScalaDatabase]]
    - [[Configuring and using JDBC | ScalaDatabase]]
    - [[Using Anorm to access your database | ScalaAnorm]]
    - [[Integrating with other database access libraries | ScalaDatabaseOthers]]
- [[Using the Cache | ScalaCache]]
    - [[The Play cache API | ScalaCache]]
- [[Calling WebServices | ScalaWS]]
    - [[The Play WS API  | ScalaWS]]
- [[Integrating with Akka | ScalaAkka]]
    - [[Setting up Actors and scheduling asynchronous tasks | ScalaAkka]]
- [[Internationalization]]
    - [[Messages externalisation and i18n]]
- [[The application Global object]]
    - [[Hooking the application lifecycle]]
    - [[Customizing error pages]]
    - [[Custom request routing]]
- [[Testing your application]]
    - [[Writing unit tests]]
    - [[Writing functional tests]]
    
## Advanced topics

- [[Handling data streams reactively]]
    - [[Iteratees]]
    - [[Enumerators and Enumeratees]]
    - [[Promises]]
    - [[Writing body parsers]]
- [[Writing a Play plugin]]
    - [[The play.api.Plugin API]]
- [[Typeclasses everywhere]]
    - [[Writing querystring and path binders]]
    - [[Handling new response formats]]
    - [[Writing new form binders]]
    - [[Writing new WebSocket frames]]
    - [[Handling new Json types]]

## Tutorials

- [[The Hello World tutorial]]
