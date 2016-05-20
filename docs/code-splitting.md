# 代码拆分
对于大型的app，把所有代码放入一个文件是比较低效的，特别是一些代码只有在某些情况下才需要加载。

Webpack 可以把你的代码拆分到“chunks”里面去，从而让你的代码可以按需加载。有些打包器把这种代码层 叫 “层”，“归纳集”，或者叫“片段”。这种处理代码的功能就叫“code splitting 代码拆分”

这是一种可选的功能，你可以在代码里面定义你的的拆分点。Webpack 会处理好依赖，输出以及运行时。

澄清一个公认的误解：代码拆分不仅仅是将公用代码提取到可共享的模块里面，更重要的是它能被用于拆分一些按需加载的模块。这样就可以保证初始文件加载变小，在应用需要的时候在加载需要的模块。


## 如何定义拆分点

AMD 和 CommonJs 有不同指定的方法去做按需加载，都支持并且和扮演拆分点的角色

### CommonJs: `require.ensure`

``` javascript
require.ensure(dependencies, callback)
```


 `require.ensure` 方法确保在每个`dependencies`中的依赖都能在`callback`调用时被异步加载。`callback`函数以`require` 作为参数执行。

例子:

``` javascript
require.ensure(["module-a", "module-b"], function(require) {
  var a = require("module-a");
  // ...
});
```

注意: `require.ensure` 只加载modules, 但不执行.

### AMD: `require`

AMD 规范定义的异步 `require` 方法如下:

``` javascript
require(dependencies, callback)
```
当被调用时，所有`dependencies`将被加载，并且`callback`将被调用参数为加载的依赖的exports。

例子:

``` javascript
require(["module-a", "module-b"], function(a, b) {
  // ...
});
```

注1: AMD `require` 加载且执行. 在webpack里 modules *从左到右* 执行.

注2: 回调函数是可以省略的.

### ES6 Modules

** Webpack 不支持 es6 modules, 直接使用 `require.ensure` 或者 `require` 取决于你是那种模块化规范.**

Webpack `1.x.x` ( `2.0.0`快来了!) 没有原生支持或兼容es6 Modules.
但是，你可以通过使用一种转换器比如`Babel`，来将ES6 `import` 语法转换成CommonJs 或者 AMD modules从而解决这个问题。这种方法是有效的但是在动态加载的时候有一个很重要的警告。


模块添加语法(`import x from 'foo'`) 故意设计成静态可分析的，也就意味着你不能做动态加载。

```javascript
// INVALID!!!!!!!!!
['lodash', 'backbone'].forEach(name => import name )
```
幸运的是，已经有一个 JS API 'loader'
Luckily, there is a JavaScript API "loader" specification being written to handle the dynamic use case: `System.load` (or `System.import`). This API will be the native equivalent to the above `require` variations. However, __most transpilers do not support converting `System.load` calls to `require.ensure`__ so you have to do that directly if you want to make use of dynamic code splitting.

```javascript
//static imports
import _ from 'lodash'

// dynamic imports
require.ensure([], function(require) {
  let contacts = require('./contacts')
})
```

## 模块内容

在拆分点的所有依赖进入到一个新的模块，依赖也被递归的添加进去。
如果你的拆分点代码传入了一个回调函数，webpack也会将回调里面的依赖自动的驾到chunk上的。


## Chunk 优化
如果两个`chunks`包涵相同的`modules`,他们将会被合并到一个，这会导致`chunks`有多个父级依赖。

如果一个`modoule`在所有的`chunk`父级可用，它将从`chunk`中被移除。

如果一个`chunk`包涵别的`chunk`的所有`modules`，这个`chunk`将被保存，并最终出现多个`chunks`




## Chunk 加载


根据设置项`target`运行环境会在bundle里面加上chunk的加载逻辑。
比如说`target`选项设置为`web`，目标chunks将通过jsonp来加载。一个chunk之加载一次并且并行的请求将被合并到一个。运行环境会检查加载后的chunk是不是多个。


## Chunk 类型

### 入口 chunk
一个入口chunk包涵了一个运行环境外加一堆modules。如果该chunk包含了module`0`，chunk将被执行。如果没有，该chunk将等到加载到包含`0`module的chunk并执行它（只要发现有含有module`0` 的chunk都执行）

### 标准 chunk
标准的chunk不包含运行时环境，仅仅包含一堆的modules。它的结构取决于chunk的加载算法。比如，对于jsonp这些模块将被包裹在一个jsonp的回调函数里面。另外标准chunk包含了一个它实现了的chunk ID 列表。

### 初始 chunk (non-entry)
一个初始的chunk是一个标准的chunk，不同的是它的优化优先级比较高，因为它像入口文件一样记入了加载时间里面。这种类型通常出现在用`CommonsChunkPlugin`合并chunk里面。



## 拆分 app 和 vendor code
拆分你的app为两个文件，`app.js`和`vendor.js`,你可以 `require`公用的文件到`vendor.js`，然后将这个文件名传入到`CommonsChunkPlugin` ，向下面这样：


``` javascript
var webpack = require("webpack");

module.exports = {
  entry: {
    app: "./app.js",
    vendor: ["jquery", "underscore", ...],
  },
  output: {
    filename: "bundle.js"
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin(/* chunkName= */"vendor", /* filename= */"vendor.bundle.js")
  ]
};
```
这样将从`app`里面移除所有的在`vendor`里面的文件。`bundle.js`将之包含你的app代码而没有他的依赖，他们将被放入`vendor.bundle.js`里面。
在你的HTML页面加载`vendor.bundle.js`（在`bundle.js`之前）即可。

``` html
<script src="vendor.bundle.js"></script>
<script src="bundle.js"></script>
```



## 多入口 chunks
在config里面配置多入口chunks是可以实现的。入口的chunk包含了运行时环境，一个页面有且仅有一个运行时环境（也可以有例外）：


### 运行多入口点
使用`CommonsChunkPlugin`后，运行环境被移动到了commons chunk 里。他们的入口点在初始chunk里面。然而只有一个初始chunk 和 多个入口chunk 能被加载，这表明在一个单页里面运行多个入口点是可行的。

例如:

``` javascript
var webpack = require("webpack");
module.exports = {
  entry: { a: "./a", b: "./b" },
  output: { filename: "[name].js" },
  plugins: [ new webpack.optimize.CommonsChunkPlugin("init.js") ]
}
```

``` html
<script src="init.js"></script>
<script src="a.js"></script>
<script src="b.js"></script>
```



## Commons chunk
`CommonsChunkPlugin`能把出现在多个入口chunk的 modules移动到一个行的入口chunk里面（commons chunk）。运行时也同样被移动到commons chunk里面。这意味着老的入口chunk成为了一个初始chunk了。可以在[plugins列表](http://stephenzhao.github.io/webpack-cn/docs/list-of-plugins.html) 看到有关配置说明。




## 优化 
有一些优化的插件可以合并chunks，看看[plugins列表](http://stephenzhao.github.io/webpack-cn/docs/list-of-plugins.html) 。


* `LimitChunkCountPlugin`
* `MinChunkSizePlugin`
* `AggressiveMergingPlugin`



##  给chunks起个别名
`require.ensure`函数可以接受额外第三个参数，这个参数必须是一个字符串。如果两个拆分点传递同样的字符串将使用相同的chunk。




## `require.include`

``` javascript
require.include(request)
```
`require.include`webpack特殊函数，目的时添加module到当前的chunk里面，但是不会执行它。（声明在bundle里面将会被干掉）


例子:

``` javascript
require.ensure(["./file"], function(require) {
  require("./file2");
});

// 等价于

require.ensure([], function(require) {
  require.include("./file");
  require("./file2");
});
```
如一个module在多个子chunk里面时候`require.include` 会很有用，在父chunk里面的`require.include`将include该module，并且在子chunk里面的module的实例将不会出现。



## 示例

* [Simple](https://github.com/webpack/webpack/tree/master/examples/code-splitting)
* [with bundle-loader](https://github.com/webpack/webpack/tree/master/examples/code-splitting-bundle-loader)
* [with context](https://github.com/webpack/webpack/tree/master/examples/code-splitted-require.context)
* [with amd and context](https://github.com/webpack/webpack/tree/master/examples/code-splitted-require.context-amd)
* [with deduplication](https://github.com/webpack/webpack/tree/master/examples/code-splitted-dedupe)
* [named-chunks](https://github.com/webpack/webpack/tree/master/examples/named-chunks)
* [multiple entry chunks](https://github.com/webpack/webpack/tree/master/examples/multiple-entry-points)
* [multiple commons chunks](https://github.com/webpack/webpack/tree/master/examples/multiple-commons-chunks)

看一个demo [example-app](http://webpack.github.io/example-app/). 可在 DevTools看一下网络请求.