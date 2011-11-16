# Writing Plugins

Play 2.0 comes with a few plugins predefined for all applications, these plugins are the following: 
* DBPlugin -> providing a JDBC datasource
* EvolutionPlugin -> provides migration  _(only available if db was configured)_
* EbeanPlugin -> provides Ebean support_ (only available if db was configured)_
* MessagesPlugin - > provides i18n support
* BasicCachePlugin-> provides in-memory caching
* GlobalPlugin -> executes application's settings

However, one can easily add a new plugin to an application.

1. first step is to implement play.api.Plugin trait which has three methods: onStart, onStop and enabled - [for example](https://github.com/playframework/Play20/blob/master/framework/play/src/main/scala/play/api/cache/Cache.scala))
2. this plugin should be available in the application either through pulling in it from a maven repository and referencing it as an app dependency or the plugin code can be part of a play application
3). you can use it directly like ```app.plugin[MyPlugin].map(_.api.mymethod).getOrElse(throwMyerror)``` (where ```app``` is  a reference to the application which can be obtain by importing play.api.Play.current) however, it's recommended to wrap it for convenience (for example, see [this](https://github.com/playframework/Play20/blob/master/framework/play/src/main/scala/play/api/cache/Cache.scala))
4) in your app, create a file: ``conf/play.plugins``` and add a reference to your plugin, just like this ```5000:com.example.MyPlugin```


_the number represents an order by setting it to > 1000 we can make sure it's loaded after global plugins_

Tip: If you are a scala developer but want to share your plugin with java developers, 

