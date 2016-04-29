# 增量构建
确保你没有操作整体重构建的话。Webpack 拥有一个强大的缓存层让你将已经构建过的模块保存在内存当中，下面的工具可以帮助你利用他们：

* [[webpack-dev-server]]: 从内存取全部webpack资源.最好的性能.
* [[webpack-dev-middleware]]: 为高级用户准备的，和webpack-dev-server同样的性能.
* [[webpack --watch | cli]] or [[`watch: true` | node.js API]]: 缓存所有资源但是写入硬盘.性能OK.

# 从解析器中排出一些资源

使用 [`noParse`](http://webpack.github.io/docs/configuration.html#module-noparse) 可以避免对一些大型库的解析构建.

# 从构建的stats中做hints

There is an [analyse tool](http://webpack.github.io/analyse/) which visualise your build and also provides some hint how build size and build performance can be optimized.

You can generate the required JSON file by running `webpack --profile --json > stats.json`

# Chunks

Generating the source file from internal representation is expensive. Each chunk is cached on it's own, but only if nothing changes in this chunk. Most chunks only depend on the included modules, but the entry chunk is also considered as dirty if the additional chunk name changes. So by using `[hash]` or `[chunkhash]` in filenames the entry chunks need to be regenerated on (nearly) every change.

By using HMR the entry chunk need to embed the hash of the compilation and is also considered as dirty on every compilation.

# SourceMaps

Perfect SourceMaps are slow.

`devtool: "source-map"` cannot cache SourceMaps for modules and need to regenerate complete SourceMap for the chunk. It's something for production.

`devtool: "eval-source-map"` is really as good as `devtool: "source-map"`, but can cache SourceMaps for modules. It's much faster for rebuilds.

`devtool: "eval-cheap-module-source-map"` offers SourceMaps that only maps lines (no column mappings) and are much faster.

`devtool: "eval-cheap-source-map"` is similar but doesn't generate SourceMaps for modules (i.e., jsx to js mappings).

`devtool: "eval"` has the best performance, but it only maps to compiled source code per module. In many cases this is good enough. (Hint: combine it with `output.pathinfo: true`.)

The UglifyJsPlugin uses SourceMaps to map errors to source code. And SourceMaps are slow. As you should only use this in production, this is fine. If your production build is *really* slow (or doesn't finish at all) you can disable it with `new UglifyJsPlugin({ sourceMap: false })`.

# `resolve.root` vs `resolve.modulesDirectories`

Only use [`resolve.modulesDirectories`](http://webpack.github.io/docs/configuration.html#resolve-modulesdirectories) for nested paths. Most paths should use [`resolve.root`](http://webpack.github.io/docs/configuration.html#resolve-root). This can give [significant performance gains](https://github.com/webpack/webpack/issues/1574#issuecomment-157520561). See also [this discussion](https://github.com/webpack/webpack/issues/472#issuecomment-55706013).

# Optimization plugins

Only use optimization plugins in production builds.

# Prefetching modules

[`prefetch`](http://webpack.github.io/docs/list-of-plugins.html#prefetchplugin)

# Dynamic linked library

If you have a bunch of rarely changing modules (i. e. vendor libs) and chunking doesn't give you enough performance (CommonsChunkPlugin), there are two plugins to create a bundle of these modules in a **separate** build step while still referencing these modules from the app bundle.

To create the DLL bundle beforehand you need to use the `DllPlugin`. Here is an [example](https://github.com/webpack/webpack/tree/master/examples/dll). This emits a public bundle and a private manifest file.

To use the DLL bundle from the app bundle you need to use the `DllReferencePlugin`. Here is an [example](https://github.com/webpack/webpack/tree/master/examples/dll-user). This stops following the dependency graph of your app when a module from the DLL bundle is found.

[webpack-dev-server]:docs/webpack-dev-server.md
[webpack-dev-middleware]:docs/webpack-dev-middleware.md
[webpack --watch | cli]:docs/cli.md
[`watch: true` | node.js API]:docs/node.js-api.md