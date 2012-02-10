# Messages externalisation and i18n 

## Specifying languages supported by your application

A valid language code is specified by a valid **ISO Language Code**, optionally followed by a valid **ISO Country Code**, such as `fr` or `en_US`.

To start you need to specify the languages supported by your application in the `conf/application.conf` file:

```
application.langs=en,en_US,fr
```

## Externalizing messages

You can externalize messages in the `conf/messages.xxx` files. 

The default `conf/messages` file matches all languages. Additionally you can specify language-specific message files like `conf/messages.fr` or `conf/messages.en_US`.

Then you can retrieve messages using the `play.api.i18n.Messages` object:

```
val title = Messages("home.title")
```

All i18n API take an implicit `play.api.i18.Lang` argument retrieved from the current scope. You can also specify it explicitly:

```
val title = Messages("home.title")(Lang("fr"))
```

> **Note:** If you have an implicit `Request` in the scope, it will provide an implicit `Lang` value corresponding to the preferred language extracted from the `Accept-Language` header and matching one the application supported languages.

## Messages format

The messages can be formatted using the `java.text.MessageFormat` library. For example:

Assuming you have message defined like:

```
files.summary=The disk {1} contains {0} file(s).
```

You can then specify parameters as:

```
Messages("files.summary", d.files.length, d.name)
```

## Retrieving supported language from an HTTP request

You can retrieve supported languages by a specific HTTP request:

```
def index = Action { request =>
  Ok("Languages: " + request.acceptLanguages.map(_.code).mkString(", "))
}
```

> **Next:** [[The application Global object | ScalaGlobal]]