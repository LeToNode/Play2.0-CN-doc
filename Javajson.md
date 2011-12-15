# JSON support

Play includes the [Jackson](http://jackson.codehaus.org/) library as a dependency _(A good tutorial of Jackson can be found [here](http://wiki.fasterxml.com/JacksonInFiveMinutes))_. What this means in practice is that one can serialize from and to JSON format. As for rendering to JSON, there is a helper ```play.json.Json.toJson``` method that can be used to send JSON as a response.

