# embedded stylesheets

Through using the `style-loader` and the `css-loader` it's possible to embed stylesheets into a webpack javascript bundle. This way you can modularize your stylesheets with your other modules. This way stylesheets are as easy as `require("./stylesheet.css")`.

## installation

Install the loaders from npm.

``` text
npm install style-loader css-loader --save-dev
```

## configuration

Here is a configuration example that enables `require()` css:

``` javascript
{
	// ...
	module: {
		loaders: [
			{ test: /\.css$/, loader: "style-loader!css-loader" }
		]
	}
}
```

> For compile-to-css languages see the according loaders for configuration examples. You can pipe them...

Keep in mind that it's difficult to manage the execution order of modules, so design your stylesheets so that order doesn't matter. (But you can rely on the order in one css file.)

## using css

``` javascript
// in your modules just require the stylesheet
// This has the side effect that a <style>-tag is added to the DOM.
require("./stylesheet.css");
```

# separate css bundle

In combination with the [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin) it's possible to generate a native css output file.

With Code Splitting we can use two different modes:

* Create one css file per initial chunk (see [[Code Splitting]]) and embed stylesheets into additional chunks. (recommended)
* Create one css file per initial chunk which also contains styles from additional chunks.

The first mode is recommended because it's optimal in regards to initial page loading time. In small apps with multiple entry points the second mode could be better because of HTTP request overheads and caching.

## plugin installation

Install the plugin from npm.

``` text
npm install extract-text-webpack-plugin --save
```

## general

To use the plugin you need to flag modules that should be moved into the css file with a special loader. After the compilation in the optimizing phase of webpack the plugin checks which modules are relevant for extraction (in the first mode only these that are in an initial chunk). These modules are compiled for node.js usage and executed to get the content. Additionally the modules are recompiled in the original bundle and replaced with an empty module.

A new asset is created for the extracted modules.

## styles from initial chunks into separate css output file

This examples shows multiple entry points, but also works with a single entry point.

``` javascript
// webpack.config.js
var ExtractTextPlugin = require("extract-text-webpack-plugin");
module.exports = {
	// The standard entry point and output config
	entry: {
		posts: "./posts",
		post: "./post",
		about: "./about"
	},
	output: {
		filename: "[name].js",
		chunkFilename: "[id].js"
	},
	module: {
		loaders: [
			// Extract css files
			{
				test: /\.css$/,
				loader: ExtractTextPlugin.extract("style-loader", "css-loader")
			},
			// Optionally extract less files
			// or any other compile-to-css language
			{
				test: /\.less$/,
				loader: ExtractTextPlugin.extract("style-loader", "css-loader!less-loader")
			}
			// You could also use other loaders the same way. I. e. the autoprefixer-loader
		]
	},
	// Use the plugin to specify the resulting filename (and add needed behavior to the compiler)
	plugins: [
		new ExtractTextPlugin("[name].css")
	]
}
```

You'll get these output files:

* `posts.js` `posts.css`
* `post.js` `post.css`
* `about.js` `about.css`
* `1.js` `2.js` (contain embedded styles)

## all styles in separate css output file

To use the second mode you just need to set the option `allChunks` to `true`:

``` javascript
// ...
module.exports = {
	// ...
	plugins: [
		new ExtractTextPlugin("style.css", {
			allChunks: true
		})
	]
}
```

You'll get these output files:

* `posts.js` `posts.css`
* `post.js` `post.css`
* `about.js` `about.css`
* `1.js` `2.js` (don't contain embedded styles)

## styles in commons chunk

You can use a separate css file in combination with the CommonsChunkPlugin. In this case a css file for the commons chunk is emitted too.

``` javascript
// ...
module.exports = {
	// ...
	plugins: [
		new webpack.optimize.CommonsChunkPlugin("commons", "commons.js"),
		new ExtractTextPlugin("[name].css")
	]
}
```

You'll get these output files:

* `commons.js` `commons.css`
* `posts.js` `posts.css`
* `post.js` `post.css`
* `about.js` `about.css`
* `1.js` `2.js` (contain embedded styles)

or with `allChunks: true`

* `1.js` `2.js` (don't contain embedded styles)