有的时候由于一些文件的模块格式不支持活着就没有好模块，webpack 不能解析这些文件，所以我们有很多的选项来讲这些文件转换成module



# [Using loaders][Using loaders]

此页例子里面的loader都写到 `require`里面了，仅仅只是为了举例。你也可以在你的webpack 里面配置。更多详情请看[Using loaders][Using loaders]



# 引入

当文件的依赖没有被`require()`的时候有用。

## [`imports-loader`](https://github.com/webpack/imports-loader)
这个loader允许你将module或其它任意格式的js添加本地文件里面
This loader allows you to put some modules or arbitrary JavaScript onto a local variable of the file.

例如: 

##### `file.js` expect a global variable `$` and you have a module `jquery` that should be used.

`require("imports?$=jquery!./file.js")`

##### `file.js` expect its configuration on a global variable `xConfig` and you want it to be `{value:123}`.

`require("imports?xConfig=>{value:123}!./file.js")`

##### `file.js` expect that `this` is the global context.

`require("imports?this=>window!./file.js")` or `require("imports?this=>global!./file.js")`

## [插件列表][list of plugins] `ProvidePlugin`

This plugin makes a module available as variable in every module. The module is required only if you use the variable.

Example: Make `$` and `jQuery` available in every module without writing `require("jquery")`.

``` javascript
new webpack.ProvidePlugin({
	$: "jquery",
	jQuery: "jquery",
	"window.jQuery": "jquery"
})
```



# 输出

当文件没有到处变量时使用。
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




# 修复损坏的module

有的文件使用错误的模块格式. 你需要告诉webpack不要使用这个格式。

## 禁用 module styles

比如:

##### 禁用 AMD

`require("imports?define=>false!./file.js")`

##### 禁用 CommonJs

`require("imports?require=>false!./file.js")`

## [configuration][configuration] 选项 `module.noParse`

This disables parsing by webpack. Therefore you cannot use dependencies. This may be useful for prepackaged libraries.

例如:

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




# 暴露全局

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




# loader的顺序

In rare cases when you have to apply more than one technique, you need to use the correct order of loaders:

inlined: `expose!imports!exports`, configuration: expose before imports before exports.

[Using loaders]: using-loaders.md
[configuration]: configuration.md