# Using Google Closure Compiler

The [Closure Compiler](http://code.google.com/p/closure-compiler/) is a tool for making JavaScript download and run faster. It is a true compiler for JavaScript - though instead of compiling from a source language to machine code, it compiles JavaScript to better JavaScript. It parses your JavaScript, analyzes it, removes dead code and rewrites and minimizes what’s left.

Any JavaScript file present in `app/assets` will be parsed by Google Closure compiler, checked for errors and dependencies and minified if activated in the build configuration.

## Check JavaScript sanity

JavaScript code is compiled during the `compile` command, as well as automatically when modified. Error are shown in the browser just like any other compilation error.

[[images/ClosureError.png]]

## CommonJS-style dependencies

Play’s Closure Compiler integration can also resolve dependencies that you declare with a CommonJS style, similarly to [RequireJS](http://requirejs.org/).

Consider the `lib.js` file:

```
// The lib

function sum(a, b) {
    return a + b;
}
exports.sum = sum;
```

And the `test.js` file:

```
// The test

require("lib");

function showSum(first, second) {
    alert(require("lib").sum(first, second));
}

showSum([2,3], 4);
```

After compilation, the file served for `test.js` will be the following:

```
require.register("lib", function(module, exports, require){ 
// The lib

function sum(a, b) {
    return a + b;
}
exports.sum = sum;

}

require("lib")

// The test

function showSum(first, second) {
    alert(require("lib").sum(first, second));
}

showSum([2,3], 4);
```

## Minification

A minified file is also generated, where `.js` is replaces by `.min.js`. In our example, it would be `test.min.js`. If you want to use the minified file instead of the regular file, you need to change the script source attribute in your HTML.

## Entry Points

By default, any JavaScript file not prepended by an underscore will be compiled. This behavior can be changed in `project/Build.scala` by overriding the `javascriptEntryPoints` key. This key holds a `PathFinder`.

For example, to compile only `.js` file from the `app/assets/javascripts/main` directory:

```
val main = PlayProject(appName, appVersion, mainLang = SCALA).settings(
   javascriptEntryPoints <<= baseDirectory(base =>
      base / "app" / "assets" / "javascripts" / "main" ** "*.js"
   )
)
```

The default definition is:

```
javascriptEntryPoints <<= (sourceDirectory in Compile)(base =>
   ((base / "assets" ** "*.js") --- (base / "assets" ** "_*")).get
)
```
