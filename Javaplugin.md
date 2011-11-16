# Writing Plugins

# Writing Plugins

Play 2.0 comes with a few plugins predefined for all applications, these plugins are the following: 

* ```DBPlugin``` -> providing a JDBC datasource
* ```EvolutionPlugin``` -> provides migration  _(only available if db was configured)_
* ```EbeanPlugin``` -> provides Ebean support_ (only available if db was configured)_
* ```MessagesPlugin``` - > provides i18n support
* ```BasicCachePlugin`` -> provides in-memory caching
* ```GlobalPlugin``` -> executes application's settings

However, one can easily add a new plugin to an application by following these steps:

1. implement ```play.Plugin``` (see [this](https://github.com/playframework/Play20/blob/master/framework/play/src/main/java/play/db/ebean/EbeanPlugin.java) for an example
2. this plugin should be available in the application either through pulling in it from a maven repository and referencing it
as an app dependency or the plugin code can be part of a play application
3. TODO
4. in your app create a file: ``conf/play.plugins``` and add a reference to your plugin, just like this ```5000:com.example.MyPlugin```
_the number represents the plugin loading order, by setting it to > 1000 we can make sure it's loaded after the global plugins_
