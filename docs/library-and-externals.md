You developed a library and want to distribute it in compiled/bundled versions (in addition to the modularized version). You want to allow the user to use it in a `<script>`-tag or with a [[amd]] loader (i. e. `require.js`). Or you depend on various precompilations and want to take the pain for the user and distribute it as simple compiled [[commonjs]] module.

## configuration options

webpack has three [[configuration]] options that are relevant for these use cases: `output.library`, `output.libraryTarget` and `externals`.

`output.libraryTarget` allows you to specify the kind to the output. I.e. CommonJs, AMD, for usage in a script tag or as UMD module.

`output.library` allows you to optionally specify a name of your library.

`externals` allows you to specify dependencies for your library that are not resolved by webpack, but become dependencies of the output. This means they are imported from the environment during runtime.

## Examples

#### compile library for usage in a `<script>`-tag

* depends on `"jquery"`, but jquery should not be included in the bundle.
* library should be available at `Foo` in the global context.

``` javascript
var jQuery = require("jquery");
var math = require("math-library");

function Foo() {}

// ...

module.exports = Foo;
```

Recommended configuration (only relevant stuff):

``` javascript
{
	output: {
		// export itself to a global var
		libraryTarget: "var",
		// name of the global var: "Foo"
		library: "Foo"
	},
	externals: {
		// require("jquery") is external and available
		//  on the global var jQuery
		"jquery": "jQuery"
	}
}
```

Resulting bundle:

``` javascript
var Foo = (/* ... webpack bootstrap ... */
({
	0: function(...) {
		var jQuery = require(1);
		/* ... */
	},
	1: function(...) {
		module.exports = jQuery;
	},
	/* ... */
});
```

## Applications and externals

You can use the `externals` options for applications too, when you want to import an existing API into the bundle. I.e. you want to use jquery from CDN (separate `<script>` tag) and still want to `require("jquery")` in your bundle. Just specify it as external: `{ externals: { jquery: "jQuery" } }`.

## Resolving and externals

Externals processing happens before resolving the request, which means you need to specify the unresolved request. Loaders are not applied to externals. You can (need to) externalize a request with loader: `require("bundle!jquery")` `{ externals: { "bundle!jquery": "bundledJQuery" } }`
