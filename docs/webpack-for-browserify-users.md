# Usage

Like browserify, webpack analyzes all the node-style `require()` calls in your app and builds a bundle that you can serve up to the browser using a `<script>` tag.

Instead of doing

``` sh
$ browserify main.js > bundle.js
```

do

``` sh
$ webpack main.js bundle.js
```

> webpack doesn't write to stdout. You need to specify a filename. It can't write to stdout because, unlike browserify, it may generate multiple output files.

---

The best way to [[configure | configuration]] webpack is with a `webpack.config.js` file. It's loaded from current directory, when running the [[executable | CLI]].

So

``` sh
$ browserify --entry main.js --outfile bundle.js
```

maps to `webpack` with this config:

``` javascript
module.exports = {
	entry: "./main.js",
	output: {
		filename: "bundle.js"
	}
}
```

> Note: A `webpack.config.js` should **export** the configuration, hence the `module.exports = {...}` in the above example.

---

## outfile

If you want to emit the output files to another directory: 

``` sh
$ browserify --outfile js/bundle.js
```

``` javascript
{
	output: {
		path: path.join(__dirname, "js"),
		filename: "bundle.js"
	}
}
```

## entry

``` sh
$ browserify --entry a.js --entry b.js
```

``` javascript
{
	entry: [
		"./a.js",
		"./b.js"
	]
}
```

## transform

browserify uses *transforms* to preprocess files. webpack uses *loaders*. Loaders are functions that take source code as an argument and return (modified) source code. Like transforms they run in node.js, can be chained, and can be asynchronous. Loaders can take additional parameters by query strings. Loaders can be used from `require()` calls. Transforms can be specified in the `package.json`. `browserify` applies configured transforms for each module. Within the webpack configuration you select the modules by RegExp. In the common case you specify loaders in the `webpack.config.js`:

``` sh
$ browserify --transform coffeeify
```

``` javascript
{
	module: {
		loaders: [
			{ test: /\.coffee$/, loader: "coffee-loader" }
		]
	}
}
```

> Note: It's possible to use browserify transforms with webpack and the [transform-loader](https://github.com/webpack/transform-loader).

## debug

``` sh
$ browserify -d
# Add inlined SourceMap
```

``` sh
$ webpack --devtool inline-source-map
# Add inlined SourceMaps

$ webpack --devtool source-map
# Emit SourceMaps as separate file

$ webpack --devtool eval
# Emit SourceUrls within evals (faster)

$ webpack --devtool eval-source-map
# Emit inlined SourceMaps within evals

$ webpack --debug
# Add more debugging information to the source

$ webpack --output-pathinfo
# Add comments about paths to source code
# (Useful when using no or the eval devtool)

$ webpack -d
# = webpack --devtool source-map --debug --output-pathinfo
```

## extension

``` sh
$ browserify --extension coffee
```

``` javascript
{
	resolve: {
		extensions: ["", ".js", ".coffee"]
	}
}
```

## standalone

``` sh
browserify --standalone MyLibrary
```

``` javascript
{
	output: {
		library: "MyLibrary",
		libraryTarget: "umd"
	}
}
// webpack --output-library MyLibrary --output-library-target umd
```

## ignore

``` sh
$ browserify --ignore file.js
```

``` javascript
{
	plugins: [
		new webpack.IgnorePlugin(/file\.js$/)
	]
}
```

## node globals

``` sh
$ browserify --insert-globals
$ browserify --detect-globals
```

You can enable/disable these node globals individually:

``` javascript
{
	node: {
		filename: true,
		dirname: "mock",
		process: false,
		global: true
	}
}
```

## ignore-missing

``` sh
$ browserify --ignore-missing
```

webpack prints errors for each missing dependency, but doesn't fail to build a bundle. You are free to ignore these errors. The `require` call will throw an error on runtime.

## noparse

``` sh
$ browserify --noparse=file.js
```

``` javascript
module.exports = {
	module: {
		noParse: [
			/file\.js$/
		]
	}
};
```

## build info

``` sh
$ browserify --deps
$ browserify --list
```

``` sh
$ webpack --json
```

## external requires

webpack does not support external requires. You cannot expose the `require` function to other scripts. Just use webpack for all scripts on a page or do it like this:

``` javascript
{
	output: {
		library: "require",
		libraryTarget: "this"
	}
}
```

``` javascript
// entry point
module.exports = function(parentRequire) {
	return function(module) {
		switch(module) {
		case "through": return require("through");
		case "duplexer": return require("duplexer");
		}
		return parentRequire(module);
	};
}(typeof __non_webpack_require__ === "function" ? __non_webpack_require__ : function() {
	throw new Error("Module '" + module + "' not found")
});
```

## multiple bundles

With browserify you can create a commons bundle that you can use in combination with bundles on multiple pages. To generate these bundles you exclude the common stuff with the `--exclude` `-x` option. Here is the example from the browserify README:

``` sh
$ browserify -r ./robot > static/common.js
$ browserify -x ./robot.js beep.js > static/beep.js
$ browserify -x ./robot.js boop.js > static/boop.js
```

webpack supports multi-page compilation and has a plugin for the automatic extraction of common modules:

``` javascript
var webpack = require("webpack");
{
	entry: {
		beep: "./beep.js",
		boop: "./boop.js",
	},
	output: {
		path: "static",
		filename: "[name].js"
	},
	plugins: [
		// ./robot is automatically detected as common module and extracted
		new webpack.optimize.CommonsChunkPlugin("common.js")
	]
}
```

``` html
<script src="common.js"></script>
<script src="beep.js"></script>
```

# API

No need to learn much more. Just pass the config object to the `webpack` API:

``` javascript
var webpack = require("webpack");

webpack({
	entry: "./main.js",
	output: {
		filename: "bundle.js"
	}
}, function(err, stats) {
	err // => fatal compiler error (rar)
	var json = stats.toJson() // => webpack --json
	json.errors // => array of errors
	json.warnings // => array of warnings
});
```

# Third-party-tool mappings

| browserify | webpack |
|------------|---------|
| watchify   | `webpack --watch` |
| browserify-middleware | [[webpack-dev-middleware]] |
| beefy | [[webpack-dev-server]] |
| deAMDify | `webpack` |
| decomponentify | [component-webpack-plugin](https://github.com/webpack/component-webpack-plugin) |
| list of source transforms | [[list of loaders]], [transform-loader](https://github.com/webpack/transform-loader) |
