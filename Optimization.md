# optimization 优化

## 最小化

为了瘦身你的js（还有你的css，如果你用到css-loader的话）webpack支持一个简单的配置项：

```--optimize-minimize ```resp.[new webpack.optimize.UglifyJsPlugin()](http://webpack.github.io/docs/list-of-plugins.html#uglifyjsplugin) 

这是一种简单而有效的方法来优化你的webapp。

如果你已经阅读过我们前面的文档，我想你已经知道webpack提供了modules 和 chunks ids 来区分他们俩。利用下面的配置项，webpack就能够比对id的使用频率和分布来得出最短的id分配给使用频率高的模块：

``` zsh
--optimize-occurence-order resp. new webpack.optimize.OccurenceOrderPlugin()

```
入口文件对于文件大小有较高的优先级（入口文件压缩优化率尽量的好）

## 去重
如果你使用了一些有着很酷的依赖树的库，那么它可能存在一些文件是重复的。webpack可以找到这些文件并去重。这保证了重复的代码不被大包到bundle文件里面去，取而代之的是运行时请求一个封装的函数。不会影响语义：
```
--optimize-dedupe resp. new webpack.optimize.DedupePlugin()

```
这个功能可能会增加入口模块的一些花销

##  对于chunks的优化

当coding的时候，你可能已经添加了许多分割点来按需加载。但编译完了之后你发现有太多细小的模块造成了很大的HTTP损耗。幸运的是Webpack可以处理这个问题，你可以做下面两件事情来合并一些请求：
* Limit the maximum chunk count with 
```
--optimize-max-chunks 15 new webpack.optimize.LimitChunkCountPlugin({maxChunks: 15})
```
* Limit the minimum chunk size with 
```
 --optimize-min-chunk-size 10000 new webpack.optimize.MinChunkSizePlugin({minChunkSize: 10000})
```
Webpack通过合并来管理这些异步加载的模块(合并更多的时候发生在当前这个chunk有复用的地方)。文件只要在入口页面加载的时候没有被引入，那么就不会被合并到chunk里面去。

## 单页

Webpack 是为单页应用量身定做的
你可以把app拆成很多chunk，这些chunk由路由来加载。入口模块仅仅包含路由和一些库，没有别的内容。这么做在用户通过导航浏览表现很好，但是初始化页面加载的时候你需要2个网络请求：一个是请求路由，一个是加载当前内容。

如果你利用HTML5的HistoryAPI 来让URL影响当前内容页的话。你的服务器可以知道那个内容页面将被客户端请求。为了节约请求数，服务端可以把要请求的内容模块放到响应头里面：以script标签的形式来添加，浏览器将并行的加载这俩请求。

```html
<script src="entry-chunk.js" type="text/javascript" charset="utf-8"></script>
<script src="3.chunk.js" type="text/javascript" charset="utf-8"></script>
```
你可以从build stas里面提取出chunk的filename （[stats-webpack-plugin ](https://www.npmjs.com/package/stats-webpack-plugin)）

## 多页

当编译一个多页面的app时，你想要在页面之间共享一些代码。这在webpack看来很简单的：只需要和多个入口文件一起编译就好
```zsh
webpack p1=./page1 p2=./page2 p3=./page3 [name].entry-chunk.js
```
```js
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3"
    },
    output: {
        filename: "[name].entry.chunk.js"
    }
}
```
由上面可以产出多个入口文件
```zsh
p1.entry.chunk.js, p2.entry.chunk.js and p3.entry.chunk.js
```
但是可以增加一个chunk来共享她们中的一些代码。
如果你的chunks有一些公用的modules，那我推荐一个很酷的插件``` CommonsChunkPlugin ```，它能辨别共用模块并把他们放倒一个文件里面去。你需要在你的页面里添加两个script标签来分别引入入口文件和共用模块文件。
``` js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3"
    },
    output: {
        filename: "[name].entry.chunk.js"
    },
    plugins: [
        new CommonsChunkPlugin("commons.chunk.js")
    ]
}

```
由上面可以产出入口文件
```zsh
p1.entry.chunk.js, p2.entry.chunk.js and p3.entry.chunk.js
```
和共用文件
```
commons.chunk.js
```
在页面中要首先加载 ```commons.chunk.js``` 在加载```xx.entry.chunk.js```
你可以出实话很多个commons chunks ，通过选择不同的入口文件。并且你可以堆叠使用这些commons chunks
``` js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3",
        ap1: "./admin/page1",
        ap2: "./admin/page2"
    },
    output: {
        filename: "[name].js"
    },
    plugins: [
        new CommonsChunkPlugin("admin-commons.js", ["ap1", "ap2"]),
        new CommonsChunkPlugin("commons.js", ["p1", "p2", "admin-commons.js"])
    ]
};
// <script>s required:
// page1.html: commons.js, p1.js
// page2.html: commons.js, p2.js
// page3.html: p3.js
// admin-page1.html: commons.js, admin-commons.js, ap1.js
// admin-page2.html: commons.js, admin-commons.js, ap2.js
```

另外你可以将多个共用文件打包到一个共用文件中。
``` js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        commons: "./entry-for-the-commons-chunk"
    },
    plugins: [
        new CommonsChunkPlugin("commons", "commons.js")
    ]
};
```
更多有关共用模块打包，可以看看：[ multiple-entry-points example](https://github.com/webpack/webpack/tree/master/examples/multiple-entry-points) 以及 [advanced multiple-commons-chunks example](https://github.com/webpack/webpack/tree/master/examples/multiple-commons-chunks)
