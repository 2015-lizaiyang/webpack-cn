# Incremental builds

Make sure you don't do a full rebuild. Webpack has a great caching layer that allows you to keep already compiled modules in memory. There are some tools that help to use it:

* [[webpack-dev-server]]: Serves all webpack assets from memory. Best performance.
* [[webpack-dev-middleware]]: The same performance as webpack-dev-server for advanced users.
* [[webpack --watch | cli]] or [[`watch: true` | node.js API]]: Caches stuff but write assets to disk. Ok performance.

# Exclude modules from parsing

With [`noParse`](http://webpack.github.io/docs/configuration.html#module-noparse) you can exclude big libraries from parsing, but this can break stuff.

# Hints from build stats

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