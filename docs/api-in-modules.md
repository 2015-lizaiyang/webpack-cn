A quick summary of all methods and variables available in code compiled with webpack.

## Basic

### `require` CommonJs

``` javascript
require(dependency: String)
```

Returns the exports from a dependency. The call is sync. No request to the server is fired. The compiler ensures that the dependency is available.

Style: CommonJs

Example:

``` javascript
var $ = require("jquery");
var myModule = require("my-module");
```

---

### `define` (with factory)

``` javascript
define([name: String], [dependencies: String[]], factoryMethod: function(...))
```

The name argument is ignored. If the `dependencies` array is provided, the factoryMethod will be called with the exports of each dependency (in the same order). If `dependencies` is not provided the factoryMethod is called with `require`, `exports` and `module` (for compatibility!). If the factoryMethod returns a value, this value is exported by the module. The call is sync. No request to the server is fired. The compiler ensures that each dependency is available.

Style: AMD

Example:

``` javascript
define(["jquery", "my-module"], function($, myModule) {
	// Do something with $ and myModule.
	// Export a function
	return function doSomething() {
		// Do something
	};
});
```

> Note: Can NOT be used in an async function.

---

### `module.exports`

This value is returned, when that module is required. It's default value is a new object.

Style: CommonJs

Example:

``` javascript
module.exports = function doSomething() {
	// Do something
};
```

> Note: Can NOT be used in an async function.

---

### `exports`

The exported object. It's the default value of `module.exports`. If `module.exports` gets overwritten, `exports` will no longer be exported.

Style: CommonJs

``` javascript
exports.someValue = 42;
exports.anObject = {
	x: 123
};
exports.aFunction = function doSomething() {
	// Do something
};
```

> Note: Using it in an async function may not have the expected effect.

---

### `define` (with value)

``` javascript
define(value: !Function)
```

Just exports the provided `value`. The `value` cannot be a function.

Style: AMD (for compatibility!)

Example: 

``` javascript
define({
	answer: 42
});
```

> Note: Can NOT be used in an async function.

---

### `export` (label)

``` javascript
export: value
```

Export the defined value. The label can occur before a function declaration or a variable declaration. The function name or variable name is the identifier under which the value is exported.

Style: Labeled modules `dependencies.LabeledModulesPlugin`

Example:

``` javascript
export: var answer = 42;
export: function method(value) {
  // Do something
};

```

> Note: Using it in an async function may not have the expected effect.

---

### `require` label

``` javascript
require: "dependency"
```

Make all exports from the dependency available in the current scope. The `require` label can occur before a string. The dependency must export values with the `export` label. CommonJs or AMD modules cannot be consumed.

Style: Labeled modules `dependencies.LabeledModulesPlugin`

Example:

``` javascript
// in dependency
export: var answer = 42;
export: function method(value) {
	// Do something
};
```

``` javascript
require: "dependency";
method(answer);
```

---

### `require.resolve`

``` javascript
require.resolve(dependency: String)
```

Returns the module id of a dependency. The call is sync. No request to the server is fired. The compiler ensures that the dependency is available.

The module id is a number in webpack (in contrast to node.js where it is a string, the filename).

Style: CommonJs

Example: 

``` javascript
var id = require.resolve("dependency");
typeof id === "number";
id === 0 // if dependency is the entry point
id > 0 // elsewise
```

---

### `module.id`

The module id of the current module.

Style: CommonJs

Example:

``` javascript
// in file.js
module.id === require.resolve("./file.js")
```

---

## Advanced

### `require.cache`

Multiple requires to the same module result in only one module execution and only one export. Therefore a cache in the runtime exists. Removing values from this cache cause new module execution and a new export. This is only needed in rare cases (for compatibility!).

Style: CommonJs

``` javascript
var d1 = require("dependency");
require("dependency") === d1
delete require.cache[require.resolve("dependency")];
require("dependency") !== d1
```

``` javascript
// in file.js
require.cache[module.id] === module
require("./file.js") === module.exports
delete require.cache[module.id];
require.cache[module.id] === undefined
require("./file.js") !== module.exports // in theory; in praxis this causes a stack overflow
require.cache[module.id] !== module
```

---

### `require.context`

``` javascript
require.context(directory:String, includeSubdirs:Boolean /* optional, default true */, filter:RegExp /* optional */)
```

Example:
```javascript
var context = require.context('components', true, /\.html$/);

var componentA = context.resolve('componentA');
```

Style: webpack

---

### `require.ensure`

``` javascript
require.ensure(dependencies: String[], callback: function([require]), [chunkName: String])
```

Download additional dependencies on demand. The `dependencies` array lists modules that should be available. When they are, `callback` is called. If the callback is a function expression, dependencies in that source part are extracted and also loaded on demand. A single request is fired to the server, except if all modules are already available.

This creates a chunk. The chunk can be named. If a chunk with this name already exists, the dependencies are merged into that chunk and that chunk is used.

Style: CommonJs

Example:

``` javascript
// in file.js
var a = require("a");
require.ensure(["b"], function(require) {
	var c = require("c");
});
require.ensure(["d"], function() {
	var e = require("e");
}, "my chunk");
require.ensure([], function() {
	var f = require("f");
}, "my chunk");
/* This results in:
	* entry chunk
		- file.js
		- a
	* anonymous chunk
		- b
		- c
	* "my chunk"
		- d
		- e
		- f
*/
```

---

### `require` AMD

``` javascript
require(dependencies: String[], [callback: function(...)])
```

Behaves similar to `require.ensure`, but the callback is called with the exports of each dependency in the `dependencies` array. There is no option to provide a chunk name.

Style: AMD

Example:

``` javascript
// in file.js
var a = require("a");
require(["b"], function(b) {
  var c = require("c");
});
/* This results in:
	* entry chunk
		- file.js
		- a
	* anonymous chunk
		- b
		- c
*/
```

---

### `require.include`

``` javascript
require.include(dependency: String)
```

Ensures that the dependency is available, but don't execute it. This can be use for optimizing the position of a module in the chunks.

Style: webpack

Example:

``` javascript
// in file.js
require.include("a");
require.ensure(["a", "b"], function(require) {
  // Do something
});
require.ensure(["a", "c"], function(require) {
  // Do something
});
/* This results in:
   * entry chunk
	 - file.js
	 - a
   * anonymous chunk
	 - b
   * anonymous chunk
	 - c
Without require.include "a" would be in both anonymous chunks.
The runtime behavior isn't changed.
*/
```

---

### `module.loaded`

This is `false` if the module is currently executing, and `false` if the sync execution has finished.

Style: node.js (for compatibility!)

---

### `module.hot`

See [[Hot Module Replacement]].

Style: webpack

---

### `global`

See [node.js global](http://nodejs.org/api/globals.html#globals_global)

Style: node.js

---

### `process`

See [node.js process](http://nodejs.org/api/process.html)

Style: node.js

---

### `__dirname`

Depending on the config option `node.__dirname`:

* `false`: Not defined
* `mock`: equal "/"
* `true`: [node.js __dirname](http://nodejs.org/api/globals.html#globals_dirname)

If used inside a expression that is parsed by the Parser, the config option is threaded as `true`.

Style: node.js (for compatibility!)

---

### `__filename`

Depending on the config option `node.__filename`:

* `false`: Not defined
* `mock`: equal "/index.js"
* `true`: [node.js __filename](http://nodejs.org/api/globals.html#globals_filename)

If used inside a expression that is parsed by the Parser, the config option is threaded as `true`.

Style: node.js (for compatibility!)

---

### `__resourceQuery`

The resource query of the current module.

Style: webpack

Example:

``` javascript
// Inside "file.js?test":
__resourceQuery === "?test"
```

---

### `__webpack_public_path__`

Equals the config options `output.publicPath`.

Style: webpack

---

### `__webpack_require__`

The raw require function. This expression isn't parsed by the Parser for dependencies.

Style: webpack

---

### `__webpack_chunk_load__`

The internal chunk loading function. Takes two arguments:

* `chunkId` The id for the chunk to load.
* `callback(require)` A callback function called once the chunk is loaded.

Style: webpack

---

### `__webpack_modules__`

Access to the internal object of all modules.

Style: webpack

---

### `require.resolveWeak`

Like `require.resolve`, but doesn't include the module into the bundle. It's a weak dependency.

Style: webpack

Example:

``` javascript
if(__webpack_modules__[require.resolveWeak("module")]) {
  // do something when module is available
}
if(require.cache[require.resolveWeak("module")]) {
  // do something when module was loaded before
}
```
---

### `__webpack_hash__`

Access to the hash of the compilation.

Only available with the `HotModuleReplacementPlugin` or the `ExtendedAPIPlugin`

Style: webpack

---

### `__non_webpack_require__`

Generates a `require` function that is not parsed by webpack. Can be used to do cool stuff with a global require function if available.

Style: webpack

---

### `DEBUG`

Equals the config option `debug`

Style: webpack