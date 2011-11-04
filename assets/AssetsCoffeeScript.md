# Using CoffeeScript

## Enabling CoffeeScript support in your application

[[CoffeeScript | http://jashkenas.github.com/coffee-script/]] is a little language that compiles into JavaScript. It provides a better syntax for writing your javascript code.

The compiled assets in Play 2.0 must be defined in the `app/assets` directory. There are handled by the build process, and coffee sources are compiled into standard JavaScript files. The generated JavaScript files are distributed as standard resources into the same `public/` folder as the unmanaged assets, meaning that there is no difference in the way you use them once compiled.

> Note that managed resources are not copied directly into your application `public` folder, but maintained in a separate folder in `target/scala-2.9.1/resources_managed`.

For example a coffe source file defined in `app/assets/javascripts/main.coffee` will be available as standard javascript resource at `public/javascripts/main.js`.

CoffeeScript sources are compiled automatically during a `compile` command, or when you refresh any page in your browser while you are running in development mode. If any compilation error occurs you will see the error in your browser:

[[coffeeError.png]]

## Layout

Here is an example layout:

```
app
  `-- assets
        `-- javascripts
               ` main.coffee   
```

The resulting JavaScript file will be compiled as `public/javascripts/main.js`, and you can use it in your template as:

```html
<script src="@routes.Assets.at("javascripts/main.js")">
```