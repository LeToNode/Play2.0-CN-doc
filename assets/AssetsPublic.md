# Serving public resources

## The public/ folder

Serving a public resource in Play 2.0 is nothing more than serving any other HTTP request. It uses the same routing and then Controller/Action path to distribute CSS, JavaScript or images files to the web client.

By convention, the public assets are stored in the `public` folder of your application. This folder is organized as:

```
public
  `-- javascripts
  `-- stylesheets
  `-- images
```

If you follow this rule it will be simpler to get started. But nothing prevent you to adapt it once you understand how it works.

## How public assets are packaged?

During the build process, the content of the `public` folder is processed and added to the application classpath. When you package your application all these file are packaged into the application jar file (under the `public/` path).

## The Assets controller

Play 2.0 comes with a built-in controller to server public assets. This controller provides automatically caching, ETag, gzip compression or Javascript minification support.

This controller is available in the default play jar as `controllers.Assets`. It defines a single `at` action with two parameters:

```
Assets.as(folder: String, file: String)
```

The `folder` parameter must be fixed and defined the directory managed by this action. The `file` parameter is usually dynamically extracted from the request path.

Here is a classical mapping of the `Assets` controller in your `conf/routes` file:

```
GET  /assets/*file        Assets.at("public", file)
```

Note that we define the `*file` dynamic part that will match the `.*` regular expression. If you send to server this request:

```
GET /assets/javascripts/jquery.js
```

The router will invoke the `Assets.at` action with the following parameter:

```
controllers.Assets.at("public", "javascripts/jquery.js")
```

This action will lookup for the file and serve it if it exists.

## Reverse routing for public assets

Like any controller mapped in the routes file, a reverse controller is created in `controllers.routes.Assets`. You can use it to reverse the URL needed to fetch a public resource. For example from a template:

```html
<script src="@routes.Assets.at("javascripts/jquery.js")"></script>
```

That will produce the following result:

```html
<script src="/assets/javascripts/jquery.js"></script>
```

Note that we don't specify the first `folder` parameter when we reverse the route. This is because our routes file defines a single mapping for the `Assets.at` action, where the `folder` parameter is fixed. So it is useless to specify it.

However if you define two mapping for the `Assets.at` action, like:

```
GET  /javascripts/*file        Assets.at("public/javascripts", file)
GET  /images/*file             Assets.at("public/images", file)
```

You will need to specify both parameter when using the reverse router:

```html
<script src="@routes.Assets.at("public/javascripts", "jquery.js")">
</script>
<image src="@routes.Assets.at("public/images", "logo.png")">
```