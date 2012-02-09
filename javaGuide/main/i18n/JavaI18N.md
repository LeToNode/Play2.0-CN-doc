# Messages externalisation and i18n 

## Specifying languages supported by your application

A valid language code is specified by a valid **ISO Language Code**, optionally followed by a valid **ISO Country Code**, such as `fr` or `en_US`.

To start you need to specify the language supported by your application in the `conf/application.conf` file:

```
application.langs=en,en_US,fr
```

## Externalizing messages

You can externalize messages in the `conf/messages.xxx` files. 

The default `conf/messages` file match all languages. Additionnally you specify by language messages file like `conf/messages.fr` or `conf/messages.en_US`.

Then you can retrieve messages using the `play.api.i18n.Messages` object:

```
String title = Messages.get("home.title")
```

You can also specify the lang explicitely:

```
String title = Messages.get(new Lang("fr"), "home.title")
```

> **Note:** If you have a `Request` in the scope, it will provide a default `Lang` value corresponding to the preferred language extracted from the `Accept-Language` header and matching one the application supported languages.

## Messages format

The messages, can be formatted using the `java.text.MessageFormat` library. For example:

Assuming you have message defined like:

```
files.summary=The disk {1} contains {0} file(s).
```

You can then specify parameters as:

```
Messages.get("files.summary", d.files.length, d.name)
```

## Retrieving supported language from an HTTP request

You can retrieve supported language by a specific HTTP request:

```
public static Result index() {
  return ok(request().acceptLanguages());
}
```

> **Next:** [[The application Global object | JavaGlobal]]