# Introduction

Loaders allow you to preprocess files as you `require()` or "load" them. Loaders are kind of like "tasks" are in other build tools, and provide a powerful way to handle frontend build steps. Loaders can transform files from a different language like, CoffeeScript to JavaScript, or inline images as data URLs. Loaders even allow you to do things like `require()` css files right in your JavaScript!

To tell Webpack to transform a module with a loader, you can specify the loader in the module __request__, such as in a `require` call.

``` javascript
var moduleWithOneLoader = require("my-loader!./my-awesome-module");
```

Notice the `!` syntax separating the loader from the module path? Loaders, like modules can also be specified with a relative path (as if you were requiring it) instead of the loader name:

```javascript
require("./loaders/my-loader!./my-awesome-module");
```

Loaders can be also be chained together by separating loaders with the `!`. This is helpful for applying multiple transformations to a file in a pipeline.

```javascript
require("style-loader!css-loader!less-loader!./my-styles.less");
```
When chaining loaders, they are applied right to left (from the file, back). In the above example, `my-styles.less` will be transformed first by the `less-loader`converting it to css, and then passed to the `css-loader` where urls, fonts, and other resources are processed, and then finally passed to `style-loader` to be transformed into a `<style>` tag.

## parameters

Loaders can accept query parameters:

``` javascript
require("loader?with=parameter!./file");
```

The format of the query string is up to the loader, so check the loaders documentation to find out about the parameters the loader accept, but generally most loaders support the traditional query string format.

## loaders by config

Specifing loaders in each module request can be brittle and repetitive. Webpack provides a way to specify which loaders apply to different file types in your Webpack [[configuration]] file. Specifying loaders in the configuration is the recommended approach in most cases as it doesn't add any build specific syntax to the code, making it more reusable.

```javascript
{
	module: {
		loaders: [
			{ test: /\.coffee$/, loader: "coffee-loader" }
		],
		preLoaders: [
			{ test: /\.coffee$/, loader: "coffee-hint-loader" }
		]
	}
};
```

See the [[configuration]] page for more information about configuring loaders.

## loader order

After the file is read from the filesystem, loaders are executed against it in the 
following order.

1. `preloaders` specified in the [[configuration]]
2. `loaders` specified in the [[configuration]]
3. loaders specified in the request (e.g. `require('raw!./file.js')`)
4. `postLoaders` specified in the [[configuration]]

You can also override the configuration loader order in the module request to suit special cases.

- adding `!` to a request will disable configured `preLoaders`
  - `require("!raw!./script.coffee")`
- adding `!!` to a request will disable all loaders specified in the configuration
  - `require("!!raw!./script.coffee")`
- adding `-!` to a request will disable configured `preLoaders` and `loaders` but not the `postLoaders`
  - `require("-!raw!./script.coffee")`

## recommendations

It is recommended that the result is **JavaScript after step 2**.

It is recommended to apply **non-JavaScript to JavaScript transformations in step 1** (or step 2 when they don't apply globally).

It is recommended to **stay in the same language in pre and post loaders**.

Source code that want to override the non-js to js transformation should use the `!` prefix. (i. e. to transform it in another way)

Using the `!!` and `-!` prefix to disable loaders is not recommended except from another loader.

* Example for a preLoader: Image compression
* Example for a loader (in config): coffee-script transformation
* Example for a loader (in request): bundle loader
* Example for a postLoader: Code coverage instrumenting


# Writing a loader

Writing a loader is pretty simple. A loader is just a file that exports a function. The compiler calls this function and passes the result of the previous loader or the resource file into it. The `this` context of the function is filled-in by the compiler with some useful methods that allow the loader to, among other things, change its invocation style to async or get query parameters. The first loader is passed one argument: the content of the resource file. The compiler expects a result from the last loader. The result should be a String or a Buffer (which is converted to a string), representing the JavaScript source code of the module. An optional SourceMap result (as JSON object) may also be passed.

A single result can be returned in sync mode. For multiple result the `this.callback` must be called. In async mode `this.async()` must be called. It returns `this.callback` if async mode is allowed. Then the loader must return `undefined` and call the callback.

Errors can be thrown in sync mode or the `this.callback` can be called with the error.

`webpack` allows async mode in every case.

`enhanced-require` allows async mode only with `require.ensure` or AMD `require`.

For more detailed instructions and guidelines, check out [[How to write a loader]].

## examples

### sync loader

``` javascript
module.exports = function(content) {
	return someSyncOperation(content);
};
```

### async loader

``` javascript
module.exports = function(content) {
	var callback = this.async();
	if(!callback) return someSyncOperation(content);
	someAsyncOperation(content, function(err, result) {
		if(err) return callback(err);
		callback(null, result);
	});
};
```

> Note: It's recommended to give an asynchronous loader a fall back to synchronous mode. This isn't required for webpack, but allows to run the loader sync using enhanced-require.

### raw loader

By default the resource file is treated as `utf-8` string and passed as String to the loader. By setting `raw` to `true` the loader is passed the raw Buffer.

Every loader is allowed to deliver its result as String or as Buffer. The compiler converts them between loaders.

``` javascript
module.exports = function(content) {
	assert(content instanceof Buffer);
	return someSyncOperation(content);
	// return value can be a Buffer too
	// This is also allowed if loader is not "raw"
};
module.exports.raw = true;
```

### pitching loader

The loaders are called from right to left. But in some cases loaders do not care about the results of the previous loader or the resource. They only care for metadata. The `pitch` method on the loaders is called from left to right before the loaders are called. If a loader delivers a result in the pitch method the process turns around and skips the remaining loaders, continuing with the calls to the more left loaders. `data` can be passed between pitch and normal call.

``` javascript
module.exports = function(content) {
	return someSyncOperation(content, this.data.value);
};
module.exports.pitch = function(remainingRequest, precedingRequest, data) {
	if(someCondition()) {
		// fast exit
		return "module.exports = require(" + JSON.stringify("-!" + remainingRequest) + ");";
	}
	data.value = 42;
};
```


## loader context

This stuff is available on `this` in a loader.

For the example this require call is used:

In `/abc/file.js`:

``` javascript
require("./loader1?xyz!loader2!./resource?rrr");
```

### `version`

Loader API version. Currently `1`.

### `context`

A string. The directory of the module. Can be used as context for resolving other stuff.

In the example: `/abc` because `resource.js` is in this directory

### `request`

The resolved request string.

In the example: `"/abc/loader1.js?xyz!/abc/node_modules/loader2/index.js!/abc/resource.js?rrr"`

### `query`

A string. The query of the request for the current loader.

In the example: in loader1: `"?xyz"`, in loader2: `""`

### `data`

A data object shared between the pitch and the normal phase.

### `cacheable`

``` javascript
cacheable(flag = true: boolean)
```

Make this loader result cacheable. By default it's not cacheable.

A cacheable loader must have a deterministic result, when inputs and dependencies haven't changed. This means the loader shouldn't have other dependencies than specified with `this.addDependency`. Most loaders are deterministic and cacheable.

### `loaders`

``` javascript
loaders = [{request: string, path: string, query: string, module: function}]
```

An array of all the loaders. It is writeable in the pitch phase.

In the example:

``` javascript
[
  { request: "/abc/loader1.js?xyz",
	path: "/abc/loader1.js",
	query: "?xyz",
	module: [Function]
  },
  { request: "/abc/node_modules/loader2/index.js",
	path: "/abc/node_modules/loader2/index.js",
	query: "",
	module: [Function]
  }
]
```

### `loaderIndex`

The index in the loaders array of the current loader.

In the example: in loader1: `0`, in loader2: `1`

### `resource`

The resource part of the request, including query.

In the example: `"/abc/resource.js?rrr"`

### `resourcePath`

The resource file.

In the example: `"/abc/resource.js"`

### `resourceQuery`

The query of the resource.

In the example: `"?rrr"`

### `emitWarning`

``` javascript
emitWarning(message: string)
```

Emit a warning.

### `emitError`

``` javascript
emitError(message: string)
```

Emit an error.

### `exec`

``` javascript
exec(code: string, filename: string)
```

Execute some code fragment like a module.

> Hint: Don't use `require(this.resourcePath)`, use this function to make loaders chainable!

### `resolve`

``` javascript
resolve(context: string, request: string, callback: function(err, result: string))
```

Resolve a request like a require expression.

### `resolveSync`

``` javascript
resolveSync(context: string, request: string) -> string
```

Resolve a request like a require expression.

### `addDependency`

``` javascript
addDependency(file: string)
dependency(file: string) // shortcut
```

Add a file as dependency of the loader result in order to make them watchable.

### `addContextDependency`

``` javascript
addContextDependency(directory: string)
```

Add a directory as dependency of the loader result.

### `clearDependencies`

``` javascript
clearDependencies()
```

Remove all dependencies of the loader result. Even initial dependencies and these of other loaders. Consider using `pitch`.

### `value`

Pass values to the next loader. If you know what your result exports if executed as module, set this value here (as a only element array).

### `inputValue`

Passed from the last loader. If you would execute the input argument as module, consider reading this variable for a shortcut (for performance).

### `options`

The options passed to the Compiler.

### `debug`

A boolean flag. It is set when in debug mode.

### `minimize`

Should the result be minimized.

### `sourceMap`

Should a SourceMap be generated.

### `target`

Target of compilation. Passed from configuration options.

Example values: `"web"`, `"node"`

### `webpack`

Set to true when this is compiled by webpack.

### `emitFile`

``` javascript
emitFile(name: string, content: Buffer|String, sourceMap: {...})
```

Emit a file. This is webpack-specific

### `_compilation`

Hacky access to the Compilation object of webpack.

### `_compiler`

Hacky access to the Compiler object of webpack.