To use components from bower you need to add two things to webpack:

* Let webpack look in the `bower_components` folder.
* Let webpack use the `main` field from the `bower.json` file.

## Configuration

See [[configuration]] `resolve.modulesDirectories` and [[list of plugins]] `ResolverPlugin`.

``` javascript
var path = require("path");
var webpack = require("webpack");
module.exports = {
	resolve: {
		modulesDirectories: ["web_modules", "node_modules", "bower_components"]
	},
	plugins: [
		new webpack.ResolverPlugin(
			new webpack.ResolverPlugin.DirectoryDescriptionFilePlugin(".bower.json", ["main"])
		)
	]
}
```

## Prefer modules from npm over bower

In many cases modules from npm are better than the same module from bower. Bower mostly contain only concatenated/bundled files which are:

* More difficult to handle for webpack
* More difficult to optimize for webpack
* Sometimes only useable without a module system

So prefer to use the CommonJs-style module and let webpack build it.

### Example react

bower package vs. npm package

> Note: the bower package is built with browserify and envify (`NODE_ENV = "production"`)

So we compare four configurations:

a) webpack + bower package (`DefinePlugin` makes no difference here as envify already removed debug code)

b) webpack + bower package + `module.noParse` for react

c) webpack + npm package

d) webpack + npm package + `DefinePlugin` with `NODE_ENV = "production"`

| configuration | modules | bundle size | compilation time |
|---------------|---------|-------------|------------------|
| a)            | 1       | 136k        | 100%             |
| b)            | 1       | 136k        | 73,6%            |
| c)            | 136     | 130k        | 89,9%            |
| d)            | 135     | 127k        | 85,3%            |

(webpack 1.3.0-beta8, react 0.10.0, bundle size minimized)