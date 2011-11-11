# Using Less CSS

## Enabling Less CSS support in your application

[[Less CSS | http://lesscss.org/]] is a dynamic stylesheet language. It allows greater flexibility in the way you write CSS files: including support for variables, mixins and more.

Compiled assets in Play 2.0 must be defined in the `app/assets` directory. They are handled by the build process, and Less sources are compiled into standard CSS files. The generated CSS files are distributed as standard resources into the same `public/` folder as the unmanaged assets, meaning that there is no difference in the way you use them once compiled.

> Note that managed resources are not copied directly into your application `public` folder, but maintained in a separate folder in `target/scala-2.9.1/resources_managed`.

For example a Less source defined into `app/assets/stylesheets/main.less` will be available as a standard resource at `public/stylesheets/main.css`.

Less sources are compiled automatically during a `compile` command, or when you refresh any page in your browser while you are running in development mode. Any compilation errors will be displayed in your browser:

[[lessError.png]]

## Working with partial Less source files

You can split your Less source into several libraries, and use the Less `import` feature. 

To avoid library files from being compiled standalone (as well as imported) we need them to be skipped by the compiler. To do this, partial source files must be prefixed with the `_` character, for example: `_myLibrary.less`.

## Layout

Here is an example layout for using Less in your project:

```
app
  `-- assets
        `-- stylesheets
               ` main.less
               ` utils
                   ` _reset.css
                   ` _layout.css    
```

With the following `main.less` source:

```css
@import "utils/_reset.less";
@import "utils/_layout.less";

h1 {
    color: red;
}
```

The resulting CSS file will be compiled as `public/stylesheets/main.css`, and you can use this in your template as any regular public asset:

```html
<link rel="stylesheet" href="@routes.Assets.at("stylesheets/main.css")">
```