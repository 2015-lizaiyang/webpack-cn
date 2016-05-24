# 什么是loader?
Loaders 是你的app里面的源文件转换器，是一种运行在nodejs里面的，以源文件的内容作为参数，返回新的转化后的内容的函数。

例如，你可以利用loaders来告诉webpack加载CoffeeScript或者JSX。

## Loader 特性
* 可以链式调用。他们在资源的管道里面被调用。最后一个loder需要输出的是JavaScript，而在中间的loader输出的可以的是能传到下一级loader的任意格式
* 可以是同步也可以是异步的
* 跑在node里面也就意味着有很多可能
* 能接受请求参数，这样就可以传入一些配置给loader
* 在源配置中可以绑定到扩展名 / RegExps对象里
* 可以通过npm发布和安装
* 标准modules就能通过`packge.json` `loader`导出除了标准`main`意外的loader
* 可以访问webpack源配置configuration
* 插件可以带给loader更多特性
* 还可以emit出其它任意格式的文件
* [更多.][loaders]
* [loaders列表][list of loaders].

# 解析 loaders
Loaders 的解析和 [modules ][resolving]类似。一个loader要求是在node里面导出的一个函数且兼容js。通常情况你可以用npm管理loader但是你也可以把它当作一个文件在你的app里面引入。
Loaders are [resolved similar to modules ][ resolving]. A loader module is expected to export a function and to be written in node.js compatible JavaScript. In the common case you manage loaders with npm, but you can also have loaders as files in your app.

## 引用 loaders
按照惯例，但也不是必须的，loader一般命名为`XXX-loader`，`XXX`代表它的上下文名字，比如`json-loader`

你可以用她的全名比如`json-loader`或者缩写`json`

Loader的命名约定和优先搜索顺序在 webpack configuration API 里的 [`resolveLoader.moduleTemplates`](http://webpack.github.io/docs/configuration.html#resolveloader-moduletemplates)中规定的。
Loader 命名约定很有用热别是在`require()`声明式里面。可以参看后面的使用方法。

## 安装 loaders

如果loader在npm里，可以这样安装:

``` sh
$ npm install xxx-loader --save
```

或者

``` sh
$ npm install xxx-loader --save-dev
```
# 使用方法

There are multiple ways to use loaders in your app:

* explicit in the `require` statement
* configured via configuration
* configured via CLI

## 用在`require`里
>**提示** 如果你希望你的脚本跨平台（nodejs和浏览器端），在可能的情况下避免使用这种方式。可以尝试使用接下来要讲到的*configuration*

在`require`表达式(或者 `define`, `require.ensure`, 等.)。

用多个loaders用`!`隔开即可，每个部分的loader的解析都相对于当前路径。

It’s possible to overwrite any loaders in the configuration by prefixing the entire rule with !.（这句没明白）

``` javascript
require("./loader!./dir/file.txt");
// => 使用 当前目录下"loader.js" 文件转换
//    在"dir"上的"file.txt".

require("jade!./template.jade");
// => 使用 "jade-loader" (安装到 "node_modules"里面的)
//    来转化"template.jade"
//   如果configuration里面还有别的loader绑定到该文件，那么那个loader会被也会调用.

require("!style!css!less!bootstrap/less/bootstrap.less");
// => 转化顺序"bootstrap.less" =>"less-loader"=>"less-loader"=>"style-loader"
```


## [Configuration][configuration]

可以将loader绑到正则里面

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

## [CLI][cli]

也可以通过CLI将loader绑定到一个扩展里面：

``` sh
$ webpack --module-bind jade --module-bind 'css=style!css'
```

上面表示 使用 "jade" 转换 ".jade" 文件， 使用 "style" 和 "css" 转换 ".css" 文件.

## 参数
Loader 可以像在web里面一样通过一个请求串来传参，请求串前面加上`?`比如`url-loader?mimetype=image/png`.
提示：请求串的格式取决于loader。可以参照loader的文档。大部分的loader都接受标准格式(`?key=value&key2=value2`)和json格式(`?{"key":"value","key2":"value2"}`)。

### `require`

``` javascript
require("url-loader?mimetype=image/png!./file.png");
```

### Configuration

``` javascript
{ test: /\.png$/, loader: "url-loader?mimetype=image/png" }
```

或者

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

[loaders]: loaders.md
[list of loaders]: list-of-loaders.md
[resolving]: resolving.md
[configuration]:configuration.md
[cli]: cli.md