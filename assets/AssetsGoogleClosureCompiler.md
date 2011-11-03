# Using Google Closure Compiler

The Closure Compiler is a tool for making JavaScript download and run faster. It is a true compiler for JavaScript. Instead of compiling from a source language to machine code, it compiles from JavaScript to better JavaScript. It parses your JavaScript, analyzes it, removes dead code and rewrites and minimizes what's left. It also checks syntax, variable references, and types, and warns about common JavaScript pitfalls.

Any Javascript file present in @app/assets@ will be parsed by Google Closure compiler, checked for errors and dependencies and minified if activated in the build configuration.

## Check Javascript Sanity

[[ClosureError.png]]

## Using Closure Compiler Annotations



## CommonJS style dependencies


## Activate minification

