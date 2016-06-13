#动机

现如今网页已经转战进入webapp时代：

* 一个页面里面有越来越多的js
* 现在的浏览器上面可以做更多的事情
* 越来越少的全页面刷新的需求，甚至有的已经是在一个页面实现所有需求。

综上，客户端已经开始拥有很高的代码量了代码量一大就需要管理，模块系统孕育而生。

##模块化风格

There are multiple standards for how to define dependencies and export values:
有很多定义依赖和加载值的表转

* `<script>`-tag 无模块系统风格
* CommonJs
* AMD 
* ES6 modules
* ...

## `<script>`-tag 风格

下面是处理没有模块系统的模块化代码的方案

``` html
<script src="module1.js"></script>
<script src="module2.js"></script>
<script src="libraryA.js"></script>
<script src="module3.js"></script>
```
每个模块都导出一个接口给一个全局变量 比如`window`。模块可以通过这个全局变量来读去依赖的接口。

#### 常见的问题

* 全局变量冲突
* 加载顺序变得麻烦
* 开发者需要手动处理依赖
* 大型项目模块列表会变得很长且难以维护 

## CommonJs:同步加载 `require`

This style uses a synchronous `require` method to load a dependency and return an exported interface. A module can specify exports by adding properties to the `exports` object or setting the value of `module.exports`.

这种风格通过同步`require` 方法加载依赖并返回一个导出的接口。一个模块可以通过添加`exports`属性活着设置`module.exports`的值来指定导出的接口。
``` javascript
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```

It's used on server-side by [node.js](http://nodejs.org).
这种方案通常见于服务端 比如nodejs
#### 优点

* 服务端的模块可重用
* 已经有很多模块用这种风格 比如npm包
* 非常简单易用

#### 缺点

* 网络请求是异步的，所以在网络请求上阻塞执行的不是很好
* 不能并行加载多个模块

#### 一些实现

* [node.js](http://nodejs.org/) - 服务端
* [browserify](https://github.com/substack/node-browserify)
* [modules-webmake](https://github.com/medikoo/modules-webmake) -编译成一个包
* [wreq](https://github.com/substack/wreq) - 服务端

## AMD: 异步加载

[`Asynchronous Module Definition`](https://github.com/amdjs/amdjs-api/wiki/AMD)

Other module systems (for the browser) had problems with the synchronous `require` (CommonJs) and introduced an asynchronous version (and a way to define modules and exporting values):

``` javascript
require(["module", "../file"], function(module, file) { /* ... */ });
define("mymodule", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
```

#### Pros

* Fits to the asynchronous request style in networks.
* Parallel loading of multiple modules.

#### Cons

* Coding overhead. More difficult to read and write.
* Seems to be some kind of workaround.

#### Implementations

* [require.js](http://requirejs.org/) - client-side
* [curl](https://github.com/cujojs/curl) - client-side

Read more about [[CommonJs]] and [[AMD]].

## ES6 modules

EcmaScript6 adds some language constructs to JavaScript, which form another module system.

``` javascript
import "jquery";
export function doStuff() {}
module "localModule" {}
```

#### Pros

* Static analysis is easy
* Future-proof as ES standard

#### Cons

* Native browser support will take time
* Very few modules in this style

## Unbiased solution

Give the developer the choice of the module style. Allow existing code to work. Make it easy to add custom module styles.

---

# Transferring

Modules should be executed on the client, so they must be transferred from the server to the browser.

There are two extremes on how to transfer modules:

* 1 request per module
* all modules in one request

Both are used in the wild, but both are suboptimal:

* 1 request per module
    * Pro: only required modules are transferred
    * Con: many requests means much overhead
    * Con: slow application startup, because of request latency
* all modules in one request
    * Pro: less request overhead, less latency
    * Con: not (yet) required modules are transferred too

## Chunked transferring

A more flexible transferring would be better. A compromise between the extremes is better in most cases.

→ While compiling all modules: Split the set of modules into multiple smaller batches (chunks).

We get multiple smaller requests. Chunks with modules that are not required initially are only requested on demand. The initial request doesn't contain your complete code base and is smaller.

The "split points" are up to the developer and optional.

→ A big code base is possible!

Note: The [idea is from Google's GWT](https://developers.google.com/web-toolkit/doc/latest/DevGuideCodeSplitting). 

Read more about [[Code Splitting]].

---

# Why only JavaScript?

Why should a module system only help the developer with JavaScript? There are many other static resources that need to be handled:

* stylesheets
* images
* webfonts
* html for templating
* etc.

And also:

* coffeescript → javascript
* elm → javascript
* less stylesheets → css stylesheets
* jade templates → javascript which generates html
* i18n files → something
* etc.

This should be as easy as:

``` javascript
require("./style.css");
```

``` javascript
require("./style.less");
require("./template.jade");
require("./image.png");
```

Read more about [[Using loaders]] and [[Loaders]].

---

# Static analysis

When compiling all the modules, a static analysis tries to find its dependencies.

Traditionally this could only find simple stuff without expression, but i.e. `require("./template/" + templateName + ".jade")` is a common construct.

Many libraries are written in different styles. Some of them are very weird...

## Strategy

A clever parser would allow most existing code to run. If the developer does something weird, it would try to find the most compatible solution.
