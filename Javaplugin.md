# Writing Plugins

# Writing Plugins

Play 2.0 comes with a few plugins predefined for all applications, these plugins are the following: 

* ```DBPlugin``` -> providing a JDBC datasource
* ```EvolutionPlugin``` -> provides migration  _(only available if db was configured)_
* ```EbeanPlugin``` -> provides Ebean support_ (only available if db was configured)_
* ```MessagesPlugin``` - > provides i18n support
* ```BasicCachePlugin`` -> provides in-memory caching
* ```GlobalPlugin``` -> executes application's settings

However, one can easily add a new plugin to an application.