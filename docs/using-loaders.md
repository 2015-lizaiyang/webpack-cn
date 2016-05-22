# 什么是loader?
Loaders 是你的app里面的源文件转换器，是一种运行在nodejs里面的，以源文件的内容作为参数，返回新的转化后的内容的函数。

例如，你可以利用loaders来告诉webpack加载CoffeeScript或者JSX。

For example, you can use loaders to tell webpack to load CoffeeScript or JSX.

## Loader 特性
* Loaders 可以链式调用。他们在资源的管道里面被调用。最后一个loder需要输出的是JavaScript，而在中间的loader输出的可以的是能传到下一级loader的任意格式
* Loaders可以是同步也可以是异步的
* Loaders can be chained. They are applied in a pipeline to the resource. The final loader is expected to return JavaScript, the others can return arbitrary format (which is passed to the next loader)
* Loaders can be synchronous and asynchronous.
* Loaders 跑在node里面也就意味着有很多可能
* 
* Loaders run in node.js and can do everything that's possible there.
* Loaders 能接受请求参数，这样就可以传入一些配置给loader
* Loaders accept query parameters. This can be used to pass configuration to the loader.
* Loaders can be bound to extension / RegExps in the configuration.
* Loaders 可以通过npm发布和安装
* Loaders can be published / installed through `npm`.
* 标准modules就能通过`packge.json` `loader`导出除了标准`main`意外的loader
* Normal modules can export a loader in addition to the normal `main` via `package.json` `loader`.
* Loaders can access the configuration.
* Loader可以访问webpack源配置configuration
* Plugins can give loaders more features.
* 插件可以带给loader更多特性
* Loaders can emit additional arbitrary files.
* Loaders 还可以emit出其它任意格式的文件
* [etc.][loaders]

If you are interested in some loader examples head off to the [list of loaders][list of loaders].

# Resolving loaders

Loaders are [[resolved similar to modules | resolving]]. A loader module is expected to export a function and to be written in node.js compatible JavaScript. In the common case you manage loaders with npm, but you can also have loaders as files in your app.

## Referencing loaders

By convention, though not required, loaders are usually named as `XXX-loader`, where `XXX` is the context name. For example, `json-loader`. 

You may reference loaders by its full (actual) name (e.g. `json-loader`), or by its shorthand name (e.g. `json`). 

The loader name convention and precedence search order is defined by [`resolveLoader.moduleTemplates`](http://webpack.github.io/docs/configuration.html#resolveloader-moduletemplates) within the webpack configuration API. 

Loader name conventions may be useful, especially when referencing them within `require()` statements; see usage below.

## Installing loaders

If the loader is available on npm you can install the loader via:

``` sh
$ npm install xxx-loader --save
```

or

``` sh
$ npm install xxx-loader --save-dev
```
# Usage

There are multiple ways to use loaders in your app:

* explicit in the `require` statement
* configured via configuration
* configured via CLI

## loaders in `require`

> **Note:** Avoid using this, if at all possible, if you intend your scripts to be environment agnostic (node.js and browser). Use the *configuration* convention for specifying loaders (see next section).

It's possible to specify the loaders in the `require` statement (or `define`, `require.ensure`, etc.). Just separate loaders from resource with `!`. Each part is resolved relative to the current directory.

It's possible to overwrite any loaders in the configuration by prefixing the entire rule with `!`.

``` javascript
require("./loader!./dir/file.txt");
// => uses the file "loader.js" in the current directory to transform
//    "file.txt" in the folder "dir".

require("jade!./template.jade");
// => uses the "jade-loader" (that is installed from npm to "node_modules")
//    to transform the file "template.jade"
//    If configuration has some transforms bound to the file, they will still be applied.

require("!style!css!less!bootstrap/less/bootstrap.less");
// => the file "bootstrap.less" in the folder "less" in the "bootstrap"
//    module (that is installed from github to "node_modules") is
//    transformed by the "less-loader". The result is transformed by the
//    "css-loader" and then by the "style-loader".
//    If configuration has some transforms bound to the file, they will not be applied.
```


## [[Configuration]]

You can bind loaders to a RegExp via configuration:

``` javascript
{
	module: {
		loaders: [
			{ test: /\.jade$/, loader: "jade" },
			// => "jade" loader is used for ".jade" files

			{ test: /\.css$/, loader: "style!css" },
			// => "style" and "css" loader is used for ".css" files
			// Alternative syntax:
			{ test: /\.css$/, loaders: ["style", "css"] },
		]
	}
}
```

## [[CLI]]

You can bind loaders to an extension via CLI:

``` sh
$ webpack --module-bind jade --module-bind 'css=style!css'
```

This uses the loader "jade" for ".jade" files and the loaders "style" and "css" for ".css" files.

## Query parameters

Loader can be passed query parameters via a query string (just like in the web). The query string is appended to the loader with `?`. i.e. `url-loader?mimetype=image/png`.

Note: The format of the query string is up to the loader. See format in the loader documentation. Most loaders accept parameters in the normal query format (`?key=value&key2=value2`) and as JSON object (`?{"key":"value","key2":"value2"}`).

### in `require`

``` javascript
require("url-loader?mimetype=image/png!./file.png");
```

### Configuration

``` javascript
{ test: /\.png$/, loader: "url-loader?mimetype=image/png" }
```

or

``` javascript
{
	test: /\.png$/,
	loader: "url-loader",
	query: { mimetype: "image/png" }
}
```


### CLI

``` sh
webpack --module-bind "png=url-loader?mimetype=image/png"
```

[loaders]: docs/loaders.md
[list of loaders]: docs/list-of-loaders