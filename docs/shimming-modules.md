有的时候由于一些文件的模块格式不支持活着就没有好模块，webpack 不能解析这些文件，所以我们有很多的选项来讲这些文件转换成module



# [Using loaders][Using loaders]

On this page all examples with loaders are inlined into `require` calls. This is just for demonstration. You may want to configure them in your webpack config instead. Read [[Using loaders]] for more details how to do this.



# Importing

Useful when a file has dependencies that are not imported via `require()`. 

## [`imports-loader`](https://github.com/webpack/imports-loader)

This loader allows you to put some modules or arbitrary JavaScript onto a local variable of the file.

Examples: 

##### `file.js` expect a global variable `$` and you have a module `jquery` that should be used.

`require("imports?$=jquery!./file.js")`

##### `file.js` expect its configuration on a global variable `xConfig` and you want it to be `{value:123}`.

`require("imports?xConfig=>{value:123}!./file.js")`

##### `file.js` expect that `this` is the global context.

`require("imports?this=>window!./file.js")` or `require("imports?this=>global!./file.js")`

## [[plugin | list of plugins]] `ProvidePlugin`

This plugin makes a module available as variable in every module. The module is required only if you use the variable.

Example: Make `$` and `jQuery` available in every module without writing `require("jquery")`.

``` javascript
new webpack.ProvidePlugin({
	$: "jquery",
	jQuery: "jquery",
	"window.jQuery": "jquery"
})
```



# Exporting

The file doesn't export its value.

## [`exports-loader`](https://github.com/webpack/exports-loader)

This loader exports variables from inside the file.

Examples:

##### The file sets a variable in the global context with `var XModule = ...`.

`var XModule = require("exports?XModule!./file.js")`

##### The file sets multiple variables in the global context with `var XParser, Minimizer`.

`var XModule = require("exports?Parser=XParser&Minimizer!./file.js"); XModule.Parser; XModule.Minimizer`

##### The file sets a global variable with `XModule = ...`.

`require("imports?XModule=>undefined!exports?XModule!./file.js")` (import to not leak to the global context)

##### The file sets a property on `window` `window.XModule = ...`.

`require("imports?window=>{}!exports?window.XModule!./file.js`




# Fixing broken module styles

Some files use a module style wrong. You may want to fix this by teaching webpack to not use this style.

## Disable some module styles

Examples:

##### Broken AMD

`require("imports?define=>false!./file.js")`

##### Broken CommonJs

`require("imports?require=>false!./file.js")`

## [[configuration]] option `module.noParse`

This disables parsing by webpack. Therefore you cannot use dependencies. This may be useful for prepackaged libraries.

Example:

``` javascript
{
	module: {
		noParse: [
			/XModule[\\\/]file\.js$/,
			path.join(__dirname, "web_modules", "XModule2")
		]
	}
}
```

> Note: `exports` and `module` are still available and usable. You may want to undefine them with the `imports-loader`.

## [`script-loader`](https://github.com/webpack/script-loader)

This loader evaluates code in the global context, just like you would add the code into a script tag. In this mode every normal library should work. `require`, `module`, etc. are undefined.

> Note: The file is added as string to the bundle. It is not minimized by webpack, so use a minimized version. There is also no dev tool support for libraries added by this loader.




# Exposing

There are cases where you want a module to export itself to the global context.

Don't do this unless you really need this. (Better use the ProvidePlugin)

## [`expose-loader`](https://github.com/webpack/expose-loader)

This loader exposes the exports to a module to the global context.

Example: 

##### Expose `file.js` as `XModule` to the global context

`require("expose?XModule!./file.js")`


Another Example: 

``` 
   require('expose?$!expose?jQuery!jquery');
   
   ...

   $(document).ready(function() {
   console.log("hey");
   })

```

By making Jquery available as a global namespace in our file contianing Jquery code or the root file, it's available to use Jquery everywhere in your project. This works very well if you plan to implement Bootstrap in your Webpack project. 

**Note:** Using too much global name-spacing will make your app less efficient. If you are planning to use a lot of global namespaces, consider implementing something like [Babel runtime](http://babeljs.io/docs/plugins/transform-runtime/) to your project. 

***




# Order of loaders

In rare cases when you have to apply more than one technique, you need to use the correct order of loaders:

inlined: `expose!imports!exports`, configuration: expose before imports before exports.

[Using loaders]: using-loaders.md