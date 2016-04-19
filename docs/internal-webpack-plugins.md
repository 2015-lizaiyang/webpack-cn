These is a list of plugins, which are internally used by webpack. You should only care about them if you are building a own compiler based on webpack, or introspect the internals.

categories of internal plugins:

* environment
* compiler
* entry
* output
* source
* optimize

## environment

Plugins affecting the environment of the compiler.

### `node/NodeEnvironmentPlugin`

Applies node.js style filesystem to the compiler.

## compiler

Plugins affecting the compiler

### `CachePlugin([cache])`

Adds a cache to the compiler, where modules are cached.

You can pass a `cache` object, where the modules are cached. Otherwise one is created per plugin instance.

### `ProgressPlugin(handler)`

Hook into the compiler to extract progress information. The `handler` must have the signature `function(percentage, message)`. It's called with `0 <= percentage <= 1`. `percentage == 0` indicates the start. `percentage == 1` indicates the end.

### `RecordIdsPlugin()`

Saves and restores module and chunk ids from records.

## entry

Plugins, which add entry chunks to the compilation.

### `SingleEntryPlugin(context, request, chunkName)`

Adds a entry chunk on compilation. The chunk is named `chunkName` and contains only one module (plus dependencies). The module is resolved from `request` in `context` (absolute path).

### `MultiEntryPlugin(context, requests, chunkName)`

Adds a entry chunk on compilation. The chunk is named `chunkName` and contains a module for each item in the `requests` array (plus dependencies). Each item in `requests` is resolved in `context` (absolute path).

### `PrefetchPlugin(context, request)`

Prefetches `request` and dependencies to enables more parallel compilation. It doesn't create any chunk. The module is resolved from `request` in `context` (absolute path).

## output

### `FunctionModulePlugin(context, options)`

Each emitted module is wrapped in a function.

`options` are the output options.

If `options.pathinfo` is set, each module function is annotated with a comment containing the module identifier shortened to `context` (absolute path).

### `JsonpTemplatePlugin(options)`

Chunks are wrapped into JSONP-calls. A loading algorithm is included in entry chunks. It loads chunks by adding a `<script>` tag.

`options` are the output options.

`options.jsonpFunction` is the JSONP function.

`options.publicPath` is uses as path for loading the chunks.

`options.chunkFilename` is the filename under that chunks are expected.

### `node/NodeTemplatePlugin(options)`

Chunks are wrapped into node.js modules exporting the bundled modules. The entry chunks loads chunks by requiring them.

`options` are the output options.

`options.chunkFilename` is the filename under that chunks are expected.

### `LibraryTemplatePlugin(name, target)`

The entries chunks are decorated to form a library `name` of type `type`.

### `webworker/WebWorkerTemplatePlugin(options)`

Chunks are loaded by `importScripts`. Else it's similar to `JsonpTemplatePlugin`.

`options` are the output options.

### `EvalDevToolModulePlugin`

Decorates the module template by wrapping each module in a `eval` annotated with `// @sourceURL`.

### `SourceMapDevToolPlugin(sourceMapFilename, sourceMappingURLComment, moduleFilenameTemplate, fallbackModuleFilenameTemplate)`

Decorates the templates by generating a SourceMap for each chunk.

`sourceMapFilename` the filename template of the SourceMap. `[hash]`, `[name]`, `[id]`, `[file]` and `[filebase]` are replaced. If this argument is missing, the SourceMap will be inlined as DataUrl.

### `NoHotModuleReplacementPlugin()`

Defines `module.hot` as `false` to remove hot module replacement code.

### `HotModuleReplacementPlugin(options)`

Add support for hot module replacement. Decorates the templates to add runtime code. Adds `module.hot` API.

`options.hotUpdateChunkFilename` The filename for hot update chunks

`options.hotUpdateMainFilename` The filename for the hot update manifest

`options.hotUpdateFunction` JSON function name for the hot update

## source

Plugins affecting the source code of modules.

### `APIPlugin`

Make `__webpack_public_path__`, `__webpack_require__`, `__webpack_modules__`, `__webpack_chunk_load__` accessible. Ensures that `require.valueOf` and `require.onError` are not processed by other plugins.

### `CompatibilityPlugin`

Currently useless. Ensures compatibility with other module loaders.

### `ConsolePlugin`

Offers a pseudo `console` if it is not available.

### `ConstPlugin`

Try to evaluate expressions in `if(...)` and replace it with `true`/`false`.

### `ProvidePlugin(name, request)`

If `name` is used in a module it is filled by a module loaded by `require(<request>)`.

### `NodeStuffPlugin(options, context)`

Provide stuff that is normally available in node.js modules.

It also ensures that `module` is filled with some node.js stuff if you use it.

### `RequireJsStuffPlugin`

Provide stuff that is normally available in require.js.

`require[js].config` is removed. `require.version` is `0.0.0`. `requirejs.onError` is mapped to `require.onError`.

### `node/NodeSourcePlugin(options)`

This module adds stuff from node.js that is not available in non-node.js environments.

It adds polyfills for `process`, `console`, `Buffer` and `global` if used. It also binds the built in Node.js replacement modules.

### `node/NodeTargetPlugin`

The plugins should be used if you run the bundle in a node.js environment.

If ensures that native modules are loaded correctly even if bundled.

### `dependencies/AMDPlugin(options)`

Provides AMD-style `define` and `require` to modules. Also bind `require.amd`, `define.amd` and `__webpack_amd_options__` to the `options` passed as parameter.

### `dependencies/CommonJsPlugin`

Provides CommonJs-style `require` to modules.

### `dependencies/LabeledModulesPlugin`

Provide labels `require:` and `exports:` to modules.

### `dependencies/RequireContextPlugin(modulesDirectories, extensions)`

Provides `require.context`. The parameter `modulesDirectories` and `extensions` are used to find alternative requests for files. It's useful to provide the same arrays as you provide to the resolver.

### `dependencies/RequireEnsurePlugin`

Provides `require.ensure`.

### `dependencies/RequireIncludePlugin`

Provides `require.include`.

### `DefinePlugin(definitions)`

Define constants for identifier.

`definitions` is an object.

## optimize

### `optimize/LimitChunkCountPlugin(options)`

Merge chunks limit chunk count is lower than `options.maxChunks`.

The overhead for each chunks is provided by `options.chunkOverhead` or defaults to 10000. Entry chunks sizes are multiplied by `options.entryChunkMultiplicator` (or 10).

Chunks that reduce the total size the most are merged first. If multiple combinations are equal the minimal merged size wins.

### `optimize/MergeDuplicateChunksPlugin`

Chunks with the same modules are merged.

### `optimize/RemoveEmptyChunksPlugin`

Modules that are included in every parent chunk are removed from the chunk.

### `optimize/MinChunkSizePlugin(minChunkSize)`

Merges chunks until each chunk has the minimum size of `minChunkSize`.

### `optimize/FlagIncludedChunksPlugin`

Adds chunk ids of chunks which are included in the chunk. This eliminates unnecessary chunk loads.

### `optimize/UglifyJsPlugin(options)`

Minimizes the chunks with `uglify.js`.

`options` are uglifyjs options.

### `optimize/OccurenceOrderPlugin(preferEntry)`

Order the modules and chunks by occurrence. This saves space, because often referenced modules and chunks get smaller ids.

`preferEntry` If true, references in entry chunks have higher priority

### `optimize/DedupePlugin`

Deduplicates modules and adds runtime code.