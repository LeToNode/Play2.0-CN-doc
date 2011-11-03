# Using Less CSS

## Enabling Less CSS support in your application

[[Less CSS | http://lesscss.org/]] is the dynamic stylesheet language. It allows more flexibility in the way you are writting your CSS files using variables, mixins and more.

The compiled assets in Play 2.0 must be defined in the `app/assets` directory. There are handled by the build process, and less sources are compiled into standard CSS files. The generated CSS files are distributed as standard resources into the same `public/` folder as the unmanaged assets, meaning that there is no difference in the way you use them once compiled.

> Note that managed resources are not copied directly into your application `public` folder, but maintained in a separate foler in `targe/scala-2.9.1/resources_managed`.

For example a less source defined into `app/assets/stylesheets/main.less` will be available as standard resource as `public/stylesheets/main.css`.

Less sources are compiled automatically during a `compile` command, or when you refresh any page in your browser while you are running in development mode. In any compilation error occurs you will see the error in your browser:

[[lessError.png]]

## Working with partial Less source files

You can split you less source into several libraries, and use the less `import` feature. 

To be skipped by the compiler, these partial less source files must be named as `_myLibrary.less`, meaning that it must start with the `_` character.

Here is an example layout:

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

The resulting CSS file will be compiled as `public/stylesheets/main.css`, that you can use in your template as:

```html
<link rel="stylesheet" media="screen" href="@routes.Assets.at("stylesheets/main.css")">
```