# Play 2.0 for Scala developers

The Scala API for Play 2.0 application developers is available in the `play.api` package. 

> The API available directly inside the `play` package (such as `play.mvc`) is reserved for Java developers. As a Scala developer, look at `play.api.mvc`.

## Main concepts

1. [[HTTP programming | ScalaActions]]
    1. [[Actions, Controllers and Results | ScalaActions]]
    1. [[HTTP routing | ScalaRouting]]
    1. [[Manipulating results | ScalaResults]]
    1. [[Session and Flash scopes | ScalaSessionFlash]]
    1. [[Body parsers | ScalaBodyParsers]]
    1. [[Actions composition | ScalaActionsComposition]]
1. [[Asynchronous HTTP programming | ScalaAsync]]
    1. [[Handling asynchronous results | ScalaAsync]]
    1. [[Streaming HTTP responses | ScalaStream]]
    1. [[Comet sockets | ScalaComet]]
    1. [[WebSockets | ScalaWebSockets]]
1. [[The template engine | ScalaTemplates]]
    1. [[Templates syntax | ScalaTemplates]]
    1. [[Common use cases | ScalaTemplateUseCases]]
1. [[HTTP form submission and validation | ScalaForms]]
    1. [[Form definitions | ScalaForms]]
    1. [[Using the form template helpers | ScalaFormHelpers]]
1. [[Working with Json | ScalaJson]]
    1. [[The Play Json library | ScalaJson]]
    1. [[Handling and serving Json requests | ScalaJsonRequests]]
1. [[Working with XML | ScalaXmlRequests]]
    1. [[Handling and serving XML requests | ScalaXmlRequests]]
1. [[Handling file upload | ScalaFileUpload]]
    1. [[Direct upload and multipart/form-data | ScalaFileUpload]]
1. [[Accessing an SQL database | ScalaDatabase]]
    1. [[Configuring and using JDBC | ScalaDatabase]]
    1. [[Using Anorm to access your database | ScalaAnorm]]
    1. [[Integrating with other database access libraries | ScalaDatabaseOthers]]
1. [[Using the Cache | ScalaCache]]
    1. [[The Play cache API | ScalaCache]]
1. [[Calling WebServices | ScalaWS]]
    1. [[The Play WS API  | ScalaWS]]
    1. [[Connecting to OpenID services | ScalaOpenID]]
    1. [[Accessing resources protected by OAuth | ScalaOAuth]]
1. [[Integrating with Akka | ScalaAkka]]
    1. [[Setting up Actors and scheduling asynchronous tasks | ScalaAkka]]
1. [[Internationalization | ScalaI18N]]
    1. [[Messages externalisation and i18n | ScalaI18N]]
1. [[The application Global object | ScalaGlobal]]
    1. [[Application global settings | ScalaGlobal]]
    1. [[Intercepting requests | ScalaInterceptors]]
1. [[Testing your application | ScalaTest]]
    1. [[Writing tests | ScalaTest]]
    1. [[Writing functional tests | ScalaFunctionalTest]]
    
## Advanced topics

1. [[Handling data streams reactively | Iteratees]]
    1. [[Iteratees | Iteratees]]
    1. [[Enumerators | Enumerators]]
    1. [[Enumeratees | Enumeratees]]
    1. [[Promises | Promises]]

## Tutorials

1. [[Your first application | ScalaTodoList]]
