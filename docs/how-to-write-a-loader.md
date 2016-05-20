## 如何编写webpack loader


webpack的loader是一个node module 导出的一个用于转化加载的资源的 `function`.

在简单的情况下，当只有一个加载器被执行的时候，其参数为资源文件的内容串。

加载器可以通过`this`上下文来访问[loader API](loaders.html)。
仅仅需要一个只值的同步加载器能很容易 `return` 它。在别的情况下，加载器可以通过`this.callback(err, values...)` 方法来返回。异常和错误会被`this.callback`传递或者被同步加载器抛出。

加载器期望返回一个或两个值，第一个是被转化的js串或者是buffer。第二个可选是js的`SourceMap` 
更复杂的情况是，当很多个加载器串联的时候，只有最后一个加载器拿到资源文件，并且只有第一个加载器能觉得返回一个还是两个值（`js` 和`SourceMap`）.其他loader的返回值都是从上一层级传递过来的。

## 例子
```js
    // Identity loader
    module.exports = function(source) {
      return source;
    };

    // Identity loader with SourceMap support
    module.exports = function(source, map) {
      this.callback(null, source, map);
    };
```

## 指南

安处理顺序排列

加载器应该具备

### 只关心一个任务

加载器可以被串联，所以要分部实现加载器，而不是一个加载器把所有事情都做了。
这也就意味着，他们没必要都被转化为js

Example: Render HTML from a template file by applying the query parameters
比如：我们要通过设置参数来将模板渲染为HTML，
不好的做法：写一个加载器编译模板，执行并返回一个包含HTML代码的模块
相反，我们应该为每一个在这个案例里的任务写加载器，并且通过管道来加载他们。


*   jade-loader: 将模板转化为一个node模块
*   apply-loader: 加载这个node模块并且按照请求返回资源
*   html-loader: 加载一个HTML文件，并且到处一个字符串模块

### 把所有模块变成模块化

Loader generated modules should respect the same design principles like normal modules.
加载器生成的模块应该遵循一般模块的设计原则，
错误的例子：（非 moduler，global state）

   ```
   require("any-template-language-loader!./xyz.atl");

   var html = anyTemplateLanguage.render("xyz");
   ```

### 把他标记为可缓存的

大多数加载器可缓存的，所以应该把他标记为可缓存的

Just call `cacheable` in the loader.
只需要调用`cacheable`函数就可以
```js
    // Cacheable identity loader
    module.exports = function(source) {
        this.cacheable();
        return source;
    };
```
### not keep state between runs and modules
### 别把让它处于代码组织者和模块之间

A loader should be independent of other modules compiled (expect of these issued by the loader).
一个加载器应该无须依赖其他模块来编译，也独立于之前编译模块

A loader should be independent of previous compilations of the same module.

### 标记好依赖

If a loader uses external resources (i. e. by reading from filesystem), they **must** tell about that. This information is used to invalidate cacheable loaders and recompile in watch mode.
如果加载器用到了额外的资源（比如说读取文件系统），那么这些资源**必须**被告知。这些信息将用于区别可缓存加载器，还是在watch状态下的重编译
```js
    // Loader adding a header
    var path = require("path");
    module.exports = function(source) {
        this.cacheable();
        var callback = this.async();
        var headerPath = path.resolve("header.js");
        this.addDependency(headerPath);
        fs.readFile(headerPath, "utf-8", function(err, header) {
            if(err) return callback(err);
            callback(null, header + "\n" + source);
        });
    };
```
### 解决依赖

在很多语言里都会有一下schema来指定依赖。比如在css中我们用`@import` `url`. 这些依赖管理应该通过模块系统解决
两点：
*  把他们转化为`require`.
*   运用`this.resolve()`来解决路径问题.

例1: 
  css-loader: 
  css-loader将依赖转换成`require`，通过`require`来替换`import`去加载别的样式表模块以及替代`url`去取资源文件.
  
例2:

  less-loader:

  less-loader不能直接将`@import`转化成`require`,因为所有的less文件需要被编译进一个通道里去跟踪`variables`和`mixins`。所以less－loader扩展了自己的路径解决逻辑。这个定制的逻辑运用了`this.resolve`函数去解决模块系统配置的文件路径。
  
如果某种语言只接受相对的urls（比如 css:`url(file)` 通常意味着`/file`)，所以有一个`~`符号来说明指向模块
``` bash
    url(file) -> require("./file")
    url(~module) -> require("module")
```
### 提取公用的模块

不要初始化很多公用的模块代码在每一个加载器执行的模块，创建一个（运行时）文件在加载器里，并且通过`require`来加载他

### 不要潜入绝对路径

不要把绝对路径放到模块的代码里面。这样当根目录被移动的时候会打破文件的哈希，有一个工具`stringifyRequest` 在`loader-utils`里面可以将绝对路径处理成相对路径

比如
```js
    var loaderUtils = require("loader-utils");
    return "var runtime = require(" +
      loaderUtils.stringifyRequest(this, "!" + require.resolve("module/runtime")) +
      ");";
```

### 将使用的库作为`peerDependencies`来依赖
使用`peerDependencies`可以允许应用开发者必要时在`package.json`去标示库的版本。依赖应该相对独立的去更新库而不用再发一个新的加载器版本
```
    "peerDependencies": {
        "library": "^1.3.5"
    }
```

### 可编程化对象作为参数

有些情况下你的加载器需要一些带有不可序列化的函数的可编程对象做为参数。
比如lessloader，提供了一个`LESS-plugins` 允许加载器去扩展webpack的`options`去检索特定的选项。为了避免名字冲突然，option是在加载器的npm驼峰名字域下的
```js
例子:

    // webpack.config.js
    module.exports = {
      ...
      lessLoader: {
        lessPlugins: [
          new LessPluginCleanCSS({advanced: true})
        ]
      }
    };
```

参考：[list of loaders](list-of-loaders.html)

## 阅读更多

 [loaders](loaders.html).