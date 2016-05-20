*Note that Hot Module Replacement (HMR) is still an experimental feature.*

# 介绍


热组件替换（HMR）是指：在应用运行时候替换、添加、移除模块而**不需要**页面重新加载

## 前提条件

* [Using Plugins]( http://webpack.github.io/docs/using-plugins.html)
* [Code Splitting](http://webpack.github.io/docs/code-splitting.html)
* [webpack-dev-server]( http://webpack.github.io/docs/webpack-dev-server.html)

## 原理
在构建的时候，webpack添加了一个小型HRM运行环境给bundle文件。这个运行环境跑在了你的app中。当构建结束Webpack并没有推出而是保持激活状态，监听资源文件的改动。如果Webpeck检测到资源文件的改动他将重新build这个改动的模块。

接下来，将根据预先的配置要么让Webpack向HRM发起通知，要么让HRM自动检测webpack的变化。任何一种方式都是将改动后的模块高速HRM运行环境来调起热更新：

首先HRM将检查是否更新的模块能自我接纳，如果不能，他将检查那些`require`过该更新模块的模块如果这些也不能接受，那就将他冒泡到其他层级，继续查找，`require`了这些`require`了变动模块的模块们直到这个更新被接受，如果到了入口点还没有，就说明热更新失败。

### 应用层

app代码会让HMR运行环境监测更新，HRM运行环境下载了更新（异步）并告诉app代码更新已经可用。app代码会通过HMR运行环境来运行更新的文件。HRM运行更新的文件（异步的）app代码可选的拉取UI进程的更新（取决于你）；


### 编译（webpack）层
除了正常的资源，编译器还需要发出‘Update’通知，来允许更新前一版本到此版本。‘Update’包涵两个内容：

1.  更新的manifest.json
2. 一个或者多个更新模块 (js)

manifest包含了新编译后的hash以及所有更新模块的列表。
更新的代码块包涵所有更新模块的代码（或者如果该模块被移除就是一个标示）.
编译器同时会确保模块和代码块id在这次build中是统一的。
编译器同时会确保模块和编译后的代码块的ID在这几次编译期间是统一的，它将这些id存在‘records’的json文件里面或者直接存在内存。

### module层

HMR is an opt-in feature, so it only affects modules that contain HMR code. The documentation describes the API that is available in modules. In general, the module developer writes handlers that are called when a dependency of this module is updated. He can also write a handler that is called when this module is updated.

In most cases it's not mandatory to write HMR code in every module. If a module has no HMR handlers the update bubbles up. This means a single handler can handle an update to a complete module tree. If a single module in this tree is updated, the complete module tree is reloaded (only reloaded not transferred).

### From the HMR runtime view (technical)

For the module system runtime is additional code emitted to track module `parents` and `children`.

On the management side the runtime supports two methods: `check` and `apply`.

A `check` does an HTTP request to the update manifest. When this request fails, there is no update available. Otherwise the list of updated chunks is compared to the list of currently loaded chunks. For each loaded chunk the corresponding update chunk is downloaded. All module updates are stored in the runtime as update. The runtime switches into the `ready` state, meaning an update has been downloaded and is ready to be applied.

For each new chunk request in the ready state the update chunk is also downloaded.

The `apply` method flags all updated modules as invalid. For each invalid module there needs to be a update handler in the module or update handlers in every parent. Else the invalid module bundles up and marks all parents as invalid too. This process continues until no more "bubbling up" occurs. If it bubbles up from an entry point the process fails.

Now all invalid modules are disposed (dispose handler) and unloaded. Then the current hash is updated and all "accept" handlers are called. The runtime switches back to the `idle` state and everything continues as normal.

### Generated files (technical)

The left side represents the initial compiler pass. The right side represents an additional pass with module 4 and 9 updated.

![generated update chunks](http://webpack.github.io/assets/HMR.svg)

## What can I do with it?

You can use it in development as a replacement for LiveReload. Actually the webpack-dev-server supports a hot mode which tries to update with HMR before trying to reload the whole page. You only need to add the `webpack/hot/dev-server` entry point and call the dev-server with `--hot`.

`webpack/hot/dev-server` will reload the entire page if the HMR update fails. If you want to [reload the page on your own](https://github.com/webpack/webpack/issues/418), you can add `webpack/hot/only-dev-server` to the entry point instead.

You can also use it in production as an updating mechanism. Here you would need to write your own management code that integrates HMR with your app.

Some loaders already generate modules that are hot-updateable (e.g. the `style-loader` can exchange a stylesheet). In these cases, you don't need to do anything special.


## What is needed to use it?

A module can only be updated if you "accept" it. So you need to `module.hot.accept` the module in the parents or the parents of the parents. For example, a router or a subview would be a good place.

If you only want to use it with the webpack-dev-server, just add `webpack/hot/dev-server` as entry point. Else you need some HMR management code that calls `check` and `apply`.

You need to enable records in the Compiler to track module id between processes. (watch mode and the webpack-dev-server keep records in memory, so you don't need it for development)

You need to enable HMR in the Compiler to let it add the HMR runtime.


## What makes it so cool?

* It's like LiveReload but for every module, so to speak.
* You can use it in production.
* The updates respect your Code Splitting and only download updates for the changed parts of your app.
* You can use it for parts of your application and it doesn't affect other modules.
* If HMR is disabled all HMR code is removed by the compiler (wrap it in `if(module.hot)`)


## Caveats

* It's experimental and not tested thoroughly.
* Expect some bugs
* Theoretically usable in production, but it maybe too early to use it for something serious
* The module ids need to be tracked between compilations so you need to store them (`records`)
* Optimizer cannot optimize module ids anymore after the first compilation. Therefore the bundle size is affected a little bit.
* HMR runtime code increases bundle size.
* For production usage additional testing is required to test the HMR handlers. This could be pretty difficult.


# Tutorial

To use hot code replacement with webpack you need four things:

* records (`--records-path`, `recordsPath: ...`)
* globally enable hot code replacement (`HotModuleReplacementPlugin`)
* hot replacement code in your code `module.hot.accept`
* hot replacement management code in your code `module.hot.check`, `module.hot.apply`

A small testcase:

``` css
/* style.css */
body {
	background: red;
}
```

``` javascript
/* entry.js */
require("./style.css");
document.write("<input type='text' />");
```

That's enough to use hot code replacement with the dev-server.

``` sh
npm install webpack webpack-dev-server -g
npm install webpack css-loader style-loader
webpack-dev-server ./entry --hot --inline --module-bind "css=style\!css"
```

The dev server provides in memory records, which is good for development.

The `--hot` switch enables hot code replacement.

> This adds the `HotModuleReplacementPlugin`. Make sure to use either the `--hot` flag, or the `HotModuleReplacementPlugin` in your `webpack.config.js`, but never both at the same time as in that case, the HMR plugin will actually be added twice, breaking the setup. 

There is special management code for the dev-server at `webpack/hot/dev-server`, which is automatically added by `--inline`. (You don't have to add it to your `webpack.config.js`)

The `style-loader` already includes hot replacement code.

If you visit [http://localhost:8080/bundle](http://localhost:8080/bundle) you should see the page with a red background and a input box. Type some text into the input box and edit `style.css` to have another background color. 

Voilà... The background updates but without full page refresh. Text and selection in the input box should stay.

Read more about how to write you own hot replacement (management) code: [[hot module replacement]]

Check the [example-app](http://webpack.github.io/example-app/) for a demo without coding. (*Note: It's a bit old, so don't look at the source code, because the HMR API changed a bit in between*)