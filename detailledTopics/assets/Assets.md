# Working with public assets

This section covers serving your application’s static resources such as JavaScript, CSS and images.

Serving a public resource in Play 2.0 is the same as serving any other HTTP request. It uses the same routing as regular resources: using the controller/action path to distribute CSS, JavaScript or image files to the client.

## The public/ folder

By convention, public assets are stored in the `public` folder of your application. This folder is organized as follows:

```
public
 └ javascripts
 └ stylesheets
 └ images
```

If you follow this structure it will be simpler to get started, but nothing stops you to modifying it once you understand how it works.

## How are public assets packaged?

During the build process, the contents of the `public` folder are processed and added to the application classpath. When you package your application, these files are packaged into the application JAR file (under the `public/` path).

## The Assets controller

Play 2.0 comes with a built-in controller to serve public assets. By default, this controller provides caching, ETag, gzip compression and JavaScript minification support.

The controller is available in the default Play JAR as `controllers.Assets`, and defines a single `at` action with two parameters:

```
Assets.at(folder: String, file: String)
```

The `folder` parameter must be fixed and defines the directory managed by the action. The `file` parameter is usually dynamically extracted from the request path.

Here is the typical mapping of the `Assets` controller in your `conf/routes` file:

```
GET  /assets/*file        Assets.at("public", file)
```

Note that we define the `*file` dynamic part that will match the `.*` regular expression. So for example, if you send this request to the server:

```
GET /assets/javascripts/jquery.js
```

The router will invoke the `Assets.at` action with the following parameters:

```
controllers.Assets.at("public", "javascripts/jquery.js")
```

This action will look-up the file and serve it, if it exists.

## Reverse routing for public assets

As for any controller mapped in the routes file, a reverse controller is created in `controllers.routes.Assets`. You use this to reverse the URL needed to fetch a public resource. For example, from a template:

```html
<script src="@routes.Assets.at("javascripts/jquery.js")"></script>
```

This will produce the following result:

```html
<script src="/assets/javascripts/jquery.js"></script>
```

Note that we don’t specify the first `folder` parameter when we reverse the route. This is because our routes file defines a single mapping for the `Assets.at` action, where the `folder` parameter is fixed. So it doesn’t need to be specified explicitly.

However, if you define two mappings for the `Assets.at` action, like this:

```
GET  /javascripts/*file        Assets.at("public/javascripts", file)
GET  /images/*file             Assets.at("public/images", file)
```

Then you will need to specify both parameters when using the reverse router:

```html
<script src="@routes.Assets.at("public/javascripts", "jquery.js")"></script>
<image src="@routes.Assets.at("public/images", "logo.png")">
```

## Etag support

The `Assets` controller automatically manages **ETag** HTTP Headers. The ETag value is generated from the resource name and the file’s last modification date. (If the resource file is embedded into a file, the JAR file’s last modification date is used.)

When a web browser makes a request specifying this **Etag**, the server can respond with **304 NotModified**.

## Gzip support

If a resource with the same name but using a `.gz` suffix is found, the `Assets` controller will serve this one by adding the proper HTTP header:

```
Content-Encoding: gzip
```

## Additional `Cache-Control` directive

Usually, using Etag is enough to have proper caching. However if you want to specify a custom `Cache-Control` header for a particular resource, you can specify it in your `application.conf` file. For example:

```
# Assets configuration
# ~~~~~
"assets.cache./public/stylesheets/bootstrap.min.css"="max-age=3600"
```

> **Next:** [[Using CoffeeScript | AssetsCoffeeScript]]