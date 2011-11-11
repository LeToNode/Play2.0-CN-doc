# Using Google Closure Compiler

The [Closure Compiler](http://code.google.com/p/closure-compiler/) is a tool for making JavaScript download and run faster. It is a true compiler for JavaScript - though instead of compiling from a source language to machine code, it compiles from JavaScript to better JavaScript. It parses your JavaScript, analyzes it, removes dead code and rewrites and minimizes what’s left.

Any Javascript file present in `app/assets` will be parsed by Google Closure compiler, checked for errors and dependencies and minified if activated in the build configuration.

## Check Javascript Sanity

Javascript code is compiled during the `compile` command, as well as automatically when modified. Error are shown in the browser just like any other compilation error.

[[ClosureError.png]]

## CommonJS style dependencies

Play’s Closure Compiler integration can also resolve dependencies that you declare with a CommonJS style, similarly to [RequireJS](http://requirejs.org/).

Consider the `lib.js` file:
```
// The lib

function sum(a, b) {
    return a + b;
}
```

And the `test.js` file:
```
// The test

require("lib");

function showSum(first, second) {
    alert(sum(first, second));
}

showSum([2,3], 4);
```

After compilation, the file served for `test.js` will be the following: 
```
// The lib

function sum(a, b) {
    return a + b;
}
// The test

function showSum(first, second) {
    alert(sum(first, second));
}

showSum([2,3], 4);
```

## Activate minification

By default minification is deactivated. You need to activate it by modifying `project/Build.scala` as follows:

```
val main = PlayProject(appName, appVersion, appDependencies)
          .settings(PlayProject.minify := true)
```
