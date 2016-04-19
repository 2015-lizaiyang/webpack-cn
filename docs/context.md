## dynamic requires

A context is created if your request contains expressions, so the exact module is not known on compile time.

Example:

``` javascript
require("./template/" + name + ".jade");
```

webpack parses the `require` statement and extracts some information:

* Directory: `./template`
* Regular expression: `/^.*\.jade$/`

## context module

A context module is generated. It contains references to all modules in that directory that can be required with a request matching the regular expression. The context module contains a map which translates requests to module ids.

Example:

``` javascript
{
	"./table.jade": 22,
	"./table-row.jade": 23,
	"./directory/folder.jade": 24
}
```

The context module also contains a bit runtime logic to access the map.

## dynamic require rewriting

The original `require` statement gets rewritten by the compiler to access the context module: (assuming the context module gets the module id `21`)

Example:

``` javascript
// original statement
require("./template/" + name + ".jade");

// rewritten statement
require(21)("./" + name + ".jade");
```

## parser evaluation

Not every expression results in a context. The parser has a small evaluation engine to evaluate simple expressions. Here are some examples:

``` javascript
require(expr ? "a" : "b"); // => require(expr ? 25 : 26)
require("a" + "b"); // => require(27)
require("not a".substr(4).replace("a", "b")); // => require(26)
// ...
```

## `require.context`

You can create your own context with the `require.context` function. It allow to pass a directory, regular expression and a flag if subdirectories should be used too.

``` javascript
require.context(directory, useSubdirectories = false, regExp = /^\.\//)
```

Examples:

``` javascript
require.context("./test", false, /Test$/)
// a context with all files from the test directory that can be
// required with a request endings with "Test"

require.context("..", true, /^grunt-[^\/]+\/tasks/[^\/]+$/)
// all grunt task that are in a modules directory of the parent folder
```

## context module API

A context module exports a (`require`) function that takes one argument: the request.

The exported function has a property `resolve` which is a function and returns the module id of the parsed request.

The exported function has another property `keys` which is a function that returns all possible requests that the context module can handle.

And the exported function has another property `id` which is the module id of the context module. This may be useful for `module.hot.accept`.

Examples:

``` javascript
var req = require.context("./templates", true, /^\.\/.*\.jade$/);

var tableTemplate = req("./table.jade");
// tableTemplate === require("./templates/table.jade");

var tableTemplateId = req.resolve("./table.jade");
// tableTemplateId === require.resolve("./templates/table.jade");

req.keys();
// is ["./table.jade", "./table-row.jade", "./directory/folder.jade"]

req.id;
// is i. e. 42
```
or

``` javascript
function requireAll(requireContext) {
  return requireContext.keys().map(requireContext);
}
// requires and returns all modules that match

var modules = requireAll(require.context("./spec", true, /^\.\/.*\.js$/));
// is an array containing all the matching modules
```

Note: `keys` depends on `Object.keys`. You may need to polyfill it for older browsers.

## `ContextReplacementPlugin`

This plugin can overwrite the details for a context (i. e. the RegExp). See [[list of plugins]].

## Critical dependencies

If the module source contains a `require` that cannot be statically analyzed, the context is the current directory.

In this case a `Critical dependencies` warning is emitted. You need to use the `ContextReplacementPlugin` in most cases.

Examples: `someFn(require)` `require.bind(null)`

## Example

See [an example here](https://github.com/webpack/webpack/tree/master/examples/require.context#examplejs).