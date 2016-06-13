> webpack 通过一个配置对象来操作. 有两种方式来传递这个对象:

### CLI
如果你使用[CLI][CLI] ，webpack 会默认读取`webpack.config.js`（或者通过 `--config` 选项指向读取文件），该文件需要导出一个配置对象。
``` javascript
module.exports = {
	// configuration
};
```

### node.js API

如果使用[node.js API][NODE] 需要将配置对象当作参数传递:

``` javascript
webpack({
	// configuration
}, callback);
```

### [](#multiple-configurations)多个配置对象

在这两种方法里面，你都可以使用一个配置对象数组来并行的执行。
他们共享数据缓存，和监听器，这样比多次执行webpack效率更高。



# 配置对象内容

> 提示: 记住不要拘泥于在配置对象里面写纯json对象，可以使用你想使用的任何js方法，他仅仅是一个nodejs模块罢了。

简单的例子:

``` javascript
{
	context: __dirname + "/app",
	entry: "./entry",
	output: {
		path: __dirname + "/dist",
		filename: "bundle.js"
	}
}
```



## `context`

用于解析`entry`选项的基础目录(绝对路径), 如果output.pathinfo设置了，就包含了缩短过的目录；（相当于公共目录，下面所有的目录都在这个公共目录下面)

> 默认: `process.cwd()`



## `entry`

bundle的入口点。
- 如果传入一个字符串，这个字符串就会被解析为启动时加载的模块。
- 如果传入个数组，所有模块都是启动时加载，模块导出到最后一个里面。
``` javascript
entry: ["./entry1", "./entry2"]
```
- 如果传入一个对象，就会创建多个输入包文件，对象键值就chunk的name，值可以是字符串或者是数组。
``` javascript
{
	entry: {
		page1: "./page1",
		page2: ["./entry1", "./entry2"]
	},
	output: {
        // 当使用多入口文件时候，要确保在output.filename使用[name]或者[id]
		filename: "[name].bundle.js",
		chunkFilename: "[id].bundle.js"
	}
}
```

> **注意**: 没有别的专门来配置入口点的选项。如果你需要一个专门来配置入口点的配置对象，你需要用到[多个配置对象](#multiple-configurations).



## `output`
 output是影响编译输出的选项。output选项告诉webpack怎么把编译文件写入磁盘。注意，虽然可以有很多输入口，但是只有一个输出配置

如果使用了哈希(`[hash]` 或者 `[chunkhash]`), 需要确保有一个一致的模块顺序。使用OccurenceOrderPlugin插件或者 recordsPath。(译者：参看[这个issue](https://github.com/webpack/webpack/issues/950))

### `output.filename`
指定输出到硬盘的文件的的文件名。这里**不能**是一个绝对的路径！`output.path`会确定该文件的存在硬盘额路径的。`filename`仅仅用来给每个文件命名而已。


**单一入口**
```javascript
{
  entry: './src/app.js',
  output: {
    filename: 'bundle.js',
    path: './built'
  }
}

// 写入磁盘: ./built/bundle.js
```

**多入口**

如果你的配置创建了多于一个的"chunk"(也就是带有多个入口点，或者使用了CommonsChunkPlugin这样的插件)，你应该使用替换符来为每个文件命名一个为一个名字。

`[name]`被chunk的名字替换.

`[hash]`被编译器hash替换.

`[chunkhash]` 被chunk的hash替换.

```javascript
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/built'
  }
}

// 谢如磁盘: ./built/app.js, ./built/search.js
```

### `output.path`

**绝对路径**  (required).

`[hash]` 被编译后文件hash替换.


### `output.publicPath`
`publicPath`指定了一个在浏览器中被引用的URL地址。
对于使用`<script>` 和 `<link>`加载器，当文件路径不同于他们的本地磁盘路径（由`path`指定）时候`publicPath`被用来作为`href`或者`url`指向该文件。这种做法在你需要将静态文件放在不同的域名或者CDN上面的时候是很有用的。 Webpack Dev Server 也是用这个方式来读取文件的。与`path`搭配使用上`[hash]`就可以做好缓存方案了。


**config.js**

``` javascript
output: {
	path: "/home/proj/public/assets",
	publicPath: "/assets/"
}

```

**index.html**
```html
<head>
  <link href="/assets/spinner.gif"/>
</head>
```
使用CDN 和 hash的例子.

**config.js**
```javascript

output: {
	path: "/home/proj/cdn/assets/[hash]",
	publicPath: "http://cdn.example.com/assets/[hash]/"
}
```
**注:** 万一最终输出文件的`publicPath`在编译的时候不知道，那么你可以不填，动态的在运行时添加也可以。如果在编译过程你不知道`publicPath`你可以忽略他，然后在你的入口文件里面添加上这个字段就可以了`__webpack_public_path__`。
```javascript
 __webpack_public_path__ = myRuntimePublicPath

// rest of your application entry
```

### `output.chunkFilename`
非入口chunk的文件名，作为一个相对路径放到`output.path`里。

`[id]` 替换chunk的id.

`[name]` 替换chunk的名字 (or 如果没有名字就用id替换).

`[hash]` 替换编译的hash.

`[chunkhash]` 替换chunk的hash.

### `output.sourceMapFilename`

js文件的SourceMap的文件名. 也同样在 `output.path` 路径下面.

`[file]` 替换js文件的文件名.

`[id]` 替换chunk的id.

`[hash]` 替换编译的hash.

> 默认: `"[file].map"`

### `output.devtoolModuleFilenameTemplate`
在生成的SourceMap里的函数`sources`数组的文件名模板。
`[resource]`替换被Webpack用来解析文件的路径，包括最右边的加载器的请求参数(如果有的话)。

`[resource-path]` 和 `[resource]`一样但是没有参数的事.

`[loaders]` 是加载器和最右加载器（显示加载器）的参数名的列表
`[all-loaders]` 是加载器和最右加载器（包括自动加载器）的参数名的列表
`[id]` 替换module的id
`[hash]`替换module标识符的hash
`[absolute-resource-path]` 替换文件绝对路径名

> 默认 (devtool=`[inline-]source-map`): `"webpack:///[resource-path]"`  
> 默认 (devtool=`eval`): `"webpack:///[resource-path]?[loaders]"`  
> 默认 (devtool=`eval-source-map`): `"webpack:///[resource-path]?[hash]"`


也可以定义成函数而不是字符串模板，该函数将接受`info`对象参数，次对象有下面几个属性：
- identifier
- shortIdentifier
- resource
- resourcePath
- absoluteResourcePath
- allLoaders
- query
- moduleId
- hash

### `output.devtoolFallbackModuleFilenameTemplate`
和`output.devtoolModuleFilenameTemplate`一样，但是用在有重复module标识符的时候。

> 默认: `"webpack:///[resourcePath]?[hash]"`

### `output.devtoolLineToLine`
为所有模块启用行映射模式，行映射模式用了一个简单的SourceMap，用在了每一行生成的source映射到原始的source，这是一个性能优化，仅用在你的性能需要更佳，你确定输入行对应生成行的时候。

`true` 用在所有模块(不建议)

可以用类似于 `module.loaders` 的带有`{test, include, exclude}` 对象 来开启特定文件.

> 默认: disabled

### `output.hotUpdateChunkFilename`

热替换chunks的文件名.
在`output.path`目录里。

`[id]` 替换chunk的id.

`[hash]` 替换编译的hash. (记录里的最近一个hash)

> 默认: `"[id].[hash].hot-update.js"`

### `output.hotUpdateMainFilename`
热替换主文件的的名字。在output.path目录里。

`[hash]` 替换编译的hash. (记录里的最近一个hash)

> 默认: `"[hash].hot-update.json"`

### `output.jsonpFunction`
webpack异步加载的JSONP函数.
较短的函数可以缩小文件的大小，在一个页面里面拥有多个webpack引用的时候，需要使用不同的标识符.

> 默认: `"webpackJsonp"`

### `output.hotUpdateFunction`

热替换时候一步更新js的jsonp方法.

> Default: `"webpackHotUpdate"`

### `output.pathinfo`

包含了一些module的信息的注解.

`require(/* ./test */23)`

不要在生产环境里面使用.

> 默认: `false`

### `output.library`

如果设置了此项, 将会把bundle打包成lib. `output.library` 的值就是文件名.

如果你在写一个单一的文件库的时候后使用他.

### `output.libraryTarget`

格式化导出的库:

`"var"` - 通过设置一个变量导出: `var Library = xxx` (default)

`"this"` - 通过设置 `this`的属性来导出: `this["Library"] = xxx`

`"commonjs"` - 通过设置 `exports`的属性导出: `exports["Library"] = xxx`

`"commonjs2"` - 通过设置 `module.exports`导出: `module.exports = xxx`

`"amd"` - 导出为AMD (视情况可通过`output.library`来命名)

`"umd"` - 导出为 AMD, CommonJS2 或者是顶级属性

> 默认: `"var"`

如 `output.library` 没有设置, 但是 `output.libraryTarget` 被设置为了`var`以外的选项, 导出的对象的每个属性都是被复制的 (除了 `amd`, `commonjs2` 和 `umd`).

### `output.umdNamedDefine`

如果 `output.libraryTarget` 被设置为 `umd` 且 `output.library` 被 设置, 设置该项为 `true` 将为AMD module 命名.

### `output.sourcePrefix`

在代码的每一行前面加上此前缀.

> 默认: `"\t"`

### `output.crossOriginLoading`

允许跨域加载chunk.

可能的值有:

`false` - 禁止.

`"anonymous"` - 可用.credentials将不随请求被发送.

`"use-credentials"` - 可用.credentials将随请求被发送.

更多请查阅 [MDN](https://developer.mozilla.org/en/docs/Web/HTML/Element/script#attr-crossorigin)

> 默认: `false`



## `module`

影响标准 module 的选项(`NormalModuleFactory`)

### `module.loaders`

自动引用的加载器的数组.

每个元素有这些选项:

* `test`: 必须满足的条件
* `exclude`: 不满足的条件
* `include`: 必须满足条件
* `loader`: 用 "!" 隔开多个loader
* `loaders`: 多个loader

可能有一项是正则表达式(测试绝对路径)，包含绝对路径的字符串，一个函数 `function(absPath): bool`，或者一个数组，用"and"结合

更多: [loaders][loaders]

*重要信息*：这里的loader解析了他们应用相关的资源，这意味着他们不需要解析配置过的文件。如果你用npm安装loaders，node_modules文件夹不在资源文件夹的父目录中，webpack就找不到这个loader。你需要把node_modules文件夹的绝对路径添加到resolveLoader.root这个选项中。 (`resolveLoader: { root: path.join(__dirname, "node_modules") }`) 

例子:

``` js
module: {
  loaders: [
    {
      // "test" is commonly used to match the file extension
      test: /\.jsx$/,

      // "include" is commonly used to match the directories
      include: [
        path.resolve(__dirname, "app/src"),
        path.resolve(__dirname, "app/test")
      ],

      // "exclude" should be used to exclude exceptions
      // try to prefer "include" when possible

      // the "loader"
      loader: "babel-loader"
    }
  ]
}
```

### `module.preLoaders`, `module.postLoaders`

 语法跟module.loaders很像，前置和后置装载的数组loaders.

### `module.noParse`
一个正则表达式或者一组正则，不会匹配到的路径
它不匹配整个解析请求。

当忽略大的库的时候可以提高性能

该文件预计不可调用require,define或者其他类似的东西，不过可以用exports和modulle.exports.

### 自动创建上下文默认值 `module.xxxContextXxx`

这有许多选项配置自动创建上下文的默认值，我们区分三种情况下自动创建的上下文:

* `exprContext`: 一个作为依赖的表达式 (如 `require(expr)`)
* `wrappedContext`: 一个加前缀或者后缀的字符串 (i. e. `require("./templates/" + expr)`)
* `unknownContext`: 一些其他不解析的 `require` (i. e. `require`)

四个选项用来自动创建上下文:

* `request`: 上下文的请求.
* `recursive`: 递归： 子目录需要被遍历.
* `regExp`:  正则表达式.
* `critical`:  这种类型的依赖应该被视为关键（发出警告）.

选项和默认值:

`unknownContextRequest = "."`, `unknownContextRecursive = true`, `unknownContextRegExp = /^\.\/.*$/`, `unknownContextCritical = true`

`exprContextRequest = "."`, `exprContextRegExp = /^\.\/.*$/`, `exprContextRecursive = true`, `exprContextCritical = true`

`wrappedContextRegExp = /.*/`, `wrappedContextRecursive = true`, `wrappedContextCritical = false`

> 注意: `module.wrappedContextRegExp` 只指完整的正则表达式的中间部分，剩下的就是从字头和字尾里产生.

例子:

``` javascript
{
  module: {
	// Disable handling of unknown requires
	unknownContextRegExp: /$^/,
	unknownContextCritical: false,

	// Disable handling of requires with a single expression
	exprContextRegExp: /$^/,
	exprContextCritical: false,

	// Warn for every expression in require
	wrappedContextCritical: true
  }
}
```



## `resolve`

影响解析模块的选项resolve.

### `resolve.alias`

模块被其他模块名和路径替代.

改配置对象键名为模块名，键值为新的路径。类似于替换但是更比替换更好。如果该键结尾是只有$的确切匹配（没有$）将被替换。

如果键值是相对路径，它将与该文件中包含的文件相对

例子: 请求 `/abc/entry.js` 里面的require ，不同的alias对比.

| `alias:` | `require("xyz")` | `require("xyz/file.js")` |
|---|---|---|
| `{}` | `/abc/node_modules/xyz/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/absolute/path/to/file.js" }` | `/absolute/path/to/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz$: "/absolute/path/to/file.js" }` | `/absolute/path/to/file.js` | error |
| `{ xyz: "./dir/file.js" }` | `/abc/dir/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz$: "./dir/file.js" }` | `/abc/dir/file.js` | error |
| `{ xyz: "/some/dir" }` | `/some/dir/index.js` | `/some/dir/file.js` |
| `{ xyz$: "/some/dir" }` | `/some/dir/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir" }` | `/abc/dir/index.js` | `/abc/dir/file.js` |
| `{ xyz: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/modu/file.js` |
| `{ xyz$: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "modu/some/file.js" }` | `/abc/node_modules/modu/some/file.js` | error |
| `{ xyz: "modu/dir" }` | `/abc/node_modules/modu/dir/index.js` | `/abc/node_modules/dir/file.js` |
| `{ xyz: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/dir/file.js` |
| `{ xyz$: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/file.js` |

`index.js` 可能会解析其他的文件，如果设置了 `package.json`的话.

`/abc/node_modules` 也可能解析到/node_modules里.

### `resolve.root`
包含你模块的目录（**绝对路径**），通常是一个目录数组，这个设置应该被用于添加个人目录到webpack查找路径里.

> 必须是个绝对路径，不要这样写./app/modules.

例子:

```javascript
var path = require('path');

// ...
resolve: {
  root: [
    path.resolve('./app/modules'),
    path.resolve('./vendor/modules')
  ]
}
```

### `resolve.modulesDirectories`

解析目录名的一个数组到当前目录以及先前的目录，并且是查找模块。这个函数和node怎么找到node_modules很像。比如如果值为["mydir"]，webpack会查找“./mydir”, “../mydir”, “../../mydir”,等等.

> 默认: `["web_modules", "node_modules"]`

> 注意: Passing `"../someDir"`, `"app"`, `"."` or an absolute path isn't necessary here. Just use a directory name, not a path. Use only if you expect to have a hierarchy within these folders. Otherwise you may want to use the `resolve.root` option instead.

### `resolve.fallback`
webpack没有在`resolve.root` 或者 `resolve.modulesDirectories`找到的模块的一个目录（或者目录绝对路径的数组）.

### `resolve.extensions`

解析模块的拓展名的数组。比如，为了发现一个CS文件，你这数组里应该包含字符串".coffee".

> 默认: `["", ".webpack.js", ".web.js", ".js"]`

**重要信息**: 设置这个选项将会重写默认值，这意味着webpack不再试着用默认的拓展名解析模块，如果你希望模块加载的时候带着他们的拓展名也可以得到正确额解析(比如require('./somefile.ext'))，你需要在你的数组里添加一个空字符串。如果你希望模块加载不带拓展名(比如require('underscore'))可以解析为“.js”的拓展名。你必须在数组里包含".js".

### `resolve.packageMains`

 在package.json中查找符合这些字段的文件.

> 默认: `["webpack", "browser", "web", "browserify", ["jam", "main"], "main"]`

### `resolve.packageAlias`

在package.json中查询对象里的字段，键值对是按照 [这个规范](https://gist.github.com/defunctzombie/4339901)的别名来进行的

> 没有默认值

比如: 比如"browser"会检查browser字段.

### `resolve.unsafeCache`

启用不安全的缓存来解析一部分文件。改变缓存路径也许会导致出错（罕见情况下）。 一个正则表达式数组里，只有一个正则或只有一个为true（对应全部文件）是最好的实践 。如果解析路径匹配，就会被缓存。
> 默认: `[]`



## `resolveLoader`

像 `resolve` 但是是对于loaders.

``` javascript
// 默认:
{
	modulesDirectories: ["web_loaders", "web_modules", "node_loaders", "node_modules"],
	extensions: ["", ".webpack-loader.js", ".web-loader.js", ".loader.js", ".js"],
	packageMains: ["webpackLoader", "webLoader", "loader", "main"]
}
```
注意，你可以用alias，其他特性和resolve相似。例如 `{ txt: 'raw-loader' }`是 `txt!templates/demo.txt`用  `raw-loader`后的结果.

### `resolveLoader.moduleTemplates`

这是resolveLoader 唯一的属性.

它描述了尝试的模块名称的替代名

> 默认: `["*-webpack-loader", "*-web-loader", "*-loader", "*"]`



## `externals`
指定的依赖不会被webpack解析，但会成为bundle里的依赖。`output.libraryTarget`.决定着依赖的类型

值是对象，字符串，函数，正则，数组都会被接受。

* 字符串：一个精确匹配的依赖会变成外部依赖，同意的字符串会被用于外部依赖。
* 对象：如果依赖精确匹配到了对象的一个属性，属性值就会被当作依赖。属性值可以包含一个依赖型的前缀，用一个空格隔开。如果属性值为true，则使用该属性名。如果属性值为false，外部测试失败，这个依赖是内部依赖。见下面的例子。
* 函数：`function(context, request, callback(err, result))`。函数会在每个依赖中调用。如果结果被传递到回调函数里，这个值就会被像处理对象属性值那样处理。
* 正则表达式：每个被匹配的依赖都会成为外部依赖。匹配的文本会被用作外部依赖的请求。因为请求是用于生成外部代码钩子的确切代码，如果你匹配到一个cmd的包(比如 `‘../some/package.js’`),相反使用外部`function`的策略。你可以通过`callback(null, "require('" + request + "')"`引入包，这个包生成`module.exports = require('../some/package.js');`使用要求在webpack上下文外。
* 数组：这个表的多个值(递归)
例如:

``` javascript
{
	output: { libraryTarget: "commonjs" },
	externals: [
		{
			a: false, // a is not external
			b: true, // b is external (require("b"))
			"./c": "c", // "./c" is external (require("c"))
			"./d": "var d" // "./d" is external (d)
		},
		// Every non-relative module is external
		// abc -> require("abc")
		/^[a-z\-0-9]+$/,
		function(context, request, callback) {
			// Every module prefixed with "global-" becomes external
			// "global-abc" -> abc
			if(/^global-/.test(request))
				return callback(null, "var " + request.substr(7));
			callback();
		},
		"./e" // "./e" is external (require("./e"))
	]
}
```

| type        | value               | resulting import code |
|-------------|---------------------|-----------------------|
| "var"       | `"abc"`             | `module.exports = abc;` |
| "var"       | `"abc.def"`         | `module.exports = abc.def;` |
| "this"      | `"abc"`             | `(function() { module.exports = this["abc"]; }());` |
| "this"      | `["abc", "def"]`    | `(function() { module.exports = this["abc"]["def"]; }());` |
| "commonjs"  | `"abc"`             | `module.exports = require("abc");` |
| "commonjs"  | `["abc", "def"]`    | `module.exports = require("abc").def;` |
| "amd"       | `"abc"`             | `define(["abc"], function(X) { module.exports = X; })` |
| "umd"       | `"abc"`             | everything above |

如果没有作为`amd/umd`的目标解析，将会执行amd或者umd的额外值.

> 注意，如果用umd你可以指定一个对象的额外值，属性为 commonjs, commonjs2, amd和root会被设置不同的值.




## `target`
编译到的目标使用环境
* `"web"` 浏览器环境(默认)
* `"webworker"` WebWorker
* `"node"` node (使用 `require` 加载 chunk)
* `"async-node"` node (使用 `fs` 和 `vm` 来加载异步chunk)
* `"node-webkit"` webkit, 使用jsonp加载chunk 但同样支持 node.js module 加， equire("nw.gui") (实验性)
* `"electron"` [Electron](http://electron.atom.io/) – 支持 `require` 带有Electron特性 modules.



## `bail`

将第一个错误报告为严重错误而不是容忍他。



## `profile`
为每一个module捕获定时信息。

> 提示: 使用 [analyze tool](http://webpack.github.io/analyse) 来做可视化分析. `--json` 或者 `stats.toJson()` 将给出states的JSON数据.



## `cache`

在多次增量编译时候，缓存生成的moudle和chunk来提高性能。

在watch模式下面默认是开启的.

你可以传`false`将它禁止掉.

你也可以传递一个对象来开启他，并且webpack会利用传入的对象作为缓存，这样你就可以在多次编译当中共享缓存对象。
注意：不要在不同的选项之间共享缓存。


## `debug`

讲loader调到debug模式.



## `devtool`

选择开发工具来提高debug效率.

* `eval` 文档上解释的很明白，每个模块都封装到 eval 包裹起来，并在后面添加 //# sourceURL
* `source-map` 这是最原始的 `source-map` 实现方式，其实现是打包代码同时创建一个新的 sourcemap 文件， 并在打包文件的末尾添加 //# sourceURL 注释行告诉 JS 引擎文件在哪儿
* `hidden-source-map` 文档上也说了，就是 soucremap 但没注释，没注释怎么找文件呢？貌似只能靠后缀，譬如 xxx/bundle.js 文件，某些引擎会尝试去找 xxx/bundle.js.map
* `inline-source-map` 为每一个文件添加 sourcemap 的 DataUrl，注意这里的文件是打包前的每一个文件而不是最后打包出来的，同时这个 DataUrl 是包含一个文件完整 souremap 信息的 Base64 格式化后的字符串，而不是一个 url。
* `eval-source-map` 这个就是把 eval 的 sourceURL 换成了完整 `souremap` 信息的 DataUrl
* `cheap-source-map` 不包含列信息，不包含 loader 的 `sourcemap`，（譬如 `babel` 的 `sourcemap`）
* `cheap-module-source-map` 不包含列信息，同时 loader 的 sourcemap 也被简化为只包含对应行的。最终的 sourcemap 只有一份，它是 webpack 对 loader 生成的 sourcemap 进行简化，然后再次生成的。

前缀 `@`, `#` 或者 `#@` 将执行编译指示风格. (默认 `#`, 推荐)

可以组合使用. `hidden`, `inline`, `eval` 标注样式是独立的.

比如. `cheap-module-inline-source-map`, `cheap-eval-source-map`, `#@source-map`

> 注意: 如果你的module已经包含了SourceMap那么你需要使用 [source-map-loader](https://github.com/webpack/source-map-loader) 将导出的sourceMap合并.

| devtool                      | 编译 | 重编译速度 | 生产环境支持 | 质量                 |
|------------------------------|-------------|---------------|----------------------|-------------------------|
| eval                         |     +++     |      +++      |       no       | generated code                |
| cheap-eval-source-map        |      +      |      ++       |       no       | transformed code (lines only) |
| cheap-source-map             |      +      |       o       |       yes      | transformed code (lines only) |
| cheap-module-eval-source-map |      o      |      ++       |       no       | original source (lines only)  |
| cheap-module-source-map      |      o      |       -       |       yes      | original source (lines only)  |
| eval-source-map              |     --      |       +       |       no       | original source               |
| source-map                   |     --      |       --      |       yes      | original source               |

例如:

``` javascript
{
	devtool: "#inline-source-map"
}
// =>
//# sourceMappingURL=...
```

> 注意: 下一个主要版本里面 `-d` 选项 将改成 `cheap-module-eval-source-map`

## `devServer`
设置 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 的相关配置。

例子:

``` javascript
{
	devServer: {
		contentBase: "./build",
	}
}
```

## `node`
包含了许多node的polyfills或者mock

* `console`: `true` 或者 `false`
* `global`: `true` 或者 `false`
* `process`: `true`, `"mock"` 或者 `false`
* `Buffer`: `true` 或者 `false`
* `__filename`: `true` (real filename), `"mock"` (`"/index.js"`) 或者 `false`
* `__dirname`: `true` (真实 dirname), `"mock"` (`"/"`) 或者 `false`
* `<node buildin>`: `true`, `"mock"`, `"empty"` 或者 `false`


``` javascript
// Default:
{
	console: false,
	global: true,
	process: true,
	Buffer: true,
	__filename: "mock",
	__dirname: "mock",
	setImmediate: true
}
```


## `amd`


设置`require.amd`和`define.amd`的值
例如: `amd: { jQuery: true }` ( 1.x AMD 版本的jQuery)



## `loader`
自定义一些在加载器上下文有用的值。



## `recordsPath`, `recordsInputPath`, `recordsOutputPath`

存储/加载 compiler状态 从/到 一个json文件里面。结果将会是一些module和chunk的固定id。

需要是 **绝对路径**，如果`recordsInputPath`,`recordsOutputPath`都为undefined，`recordsInputPath`将被使用。

在多个编译请求做热替换的时候是需要这个配置的。




## `plugins`

给编译器添加额外的插件.

[CLI]:docs/cli.md
[NODE]:docs/node.js-api.md
[loaders]:docs/loaders.md