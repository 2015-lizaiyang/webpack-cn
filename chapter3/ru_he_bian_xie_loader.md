## 如何书写webpack loader


webpack的loader是一个node module 导出的一个用于转化加载的资源的 `function`.

In the simple case, when only a single loader is applied to the resource, the loader is called with one parameter: the content of the resource file as string.
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

In many languages there is some schema to specify dependencies. i. e. in css there is `@import` and `url(...)`. These dependencies should be resolved by the module system.

There are two options to do this:

*   Transform them to `require`s.
*   Use the `<span class="keyword">this</span>.resolve` function to resolve the path

Example 1 css-loader: The css-loader transform dependencies to `require`s, by replacing `@import`s with a require to the other stylesheet (processed with the css-loader too) and `url(...)` with a `require` to the referenced file.

Example 2 less-loader: The less-loader cannot transform `@import`s to `require`s, because all less files need to be compiled in one pass to track variables and mixins. Therefore the less-loader extends the less compiler with a custom path resolving logic. This custom logic uses `<span class="keyword">this</span>.resolve` to resolve the file with the configuration of the module system (aliasing, custom module directories, etc.).

If the language only accept relative urls (like css: `url(file)` always means `.<span class="regexp">/file</span>`), there is the `~`-convection to specify references to modules:

    url(file) -> require("./file")
    url(~module) -> require("module")

### [→](#extract-common-code)extract common code

don’t generate much code that is common in every module processed by that loader. Create a (runtime) file in the loader and generate a `require` to that common code.

### [→](#should-not-embed-absolute-paths)should not embed absolute paths

don’t put absolute paths in to the module code. They break hashing when the root for the project is moved. There is a method [`stringifyRequest` in loader-utils](https://github.com/webpack/loader-utils#stringifyrequest) which converts an absolute path to an relative one.

Example:

    var loaderUtils = require("loader-utils");
    return "var runtime = require(" +
      loaderUtils.stringifyRequest(this, "!" + require.resolve("module/runtime")) +
      ");";

### [→](#use-a-library-as-peerdependencies-when-they-wrap-it)use a library as `peerDependencies` when they wrap it

using a peerDependency allows the application developer to specify the exact version in `package.json` if desired. The dependency should be relatively open to allow updating the library without needing to publish a new loader version.

    "peerDependencies": {
        "library": "^1.3.5"
    }

### [→](#programmable-objects-as-query-option)programmable objects as `query`-option

there are situations where your loader requires programmable objects with functions which cannot stringified as `query`-string. The less-loader, for example, provides the possibility to specify [LESS-plugins](https://github.com/webpack/less-loader#less-plugins). In these cases, a loader is allowed to extend webpack’s `options`-object to retrieve that specific option. In order to avoid name collisions, however, it is important that the option is namespaced under the loader’s camelCased npm-name.

Example:

    // webpack.config.js
    module.exports = {
      ...
      lessLoader: {
        lessPlugins: [
          new LessPluginCleanCSS({advanced: true})
        ]
      }
    };

The loader should also allow to specify the config-key (e.g. `lessLoader`) via `query`. See [discussion](https://github.com/webpack/less-loader/pull/40) and [example implementation](https://github.com/webpack/less-loader/blob/39f742b4624fceae6d9cf266e9554d07a32a9c14/index.js#L49-51).

### [→](#be-added-to-the-list-of-loaders)be added to the [list of loaders](list-of-loaders.html)

## [→](#read-more)Read more

Read more about [loaders](loaders.html).