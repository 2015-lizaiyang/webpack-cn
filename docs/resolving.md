The resolving process is pretty simple. We distinguish three types of requests:

* absolute path: `require("/home/me/file")` `require("C:\Home\me\file")`
* relative path: `require("../src/file")` `require("./file")`
* module path: `require("module")` `require("module/lib/file")`

## Resolving an absolute path

We first check if the path points to a directory. For a directory we need to find the main file in this directory. Therefore the `main` field in the `package.json` is joined to the path. If there is no `package.json` or no `main` field, `index` is used as filename.

We have an absolute path to a file now. We try to append all extensions (configuration option `resolve.extensions`). The first existing file is used as result.

## Resolving a relative path

The context directory is the directory of the resource file that contains the `require` statement. If there is no resource file the configuration option `context` is used as context directory. (This can occur for entry points or with loader-generated files).

The relative path is joined to the context directory and the resulting absolute file is resolved according to "Resolving an absolute path".

## Resolving a module path

For resolving a module we first gather all search directories for modules from the context directory. This process is similar to the [node.js resolving process](http://nodejs.org/api/modules.html), but the search directories are configurable with the configuration option `resolve.modulesDirectories`. In addition to this the directories in the configuration option `resolve.root` are prepended and directories in the configuration option `resolve.fallback` are appended.

The module is looked up in each module directory and resolved according to "Resolving an absolute path". If the first match has no success, the second is tried and so on.

## Aliasing

There is a configuration option `resolve.alias` which renames modules.

When trying to "resolve a module path" the module name is matched to the `resolve.alias` option and when there is a match it gets replaced with the alias.

## Caching

Every filesystem access is cached so that multiple parallel or serial requests to the same thing are merged. In watching mode only changed files are removed from cache (the watcher knows which files got changed). In non-watching mode the cache is purged before every compilation.

## Unsafe caching

There is a configuration option `resolve.unsafeCache` which boosts performance by aggressive caching.

Every resolve process is cached and isn't ever purged. This is correct in most cases, but incorrect in edge cases (*what edge cases?*).

## Context

When trying to resolve a [[context]] "Resolving an absolute path" ends when a directory is found.

## Loaders

For loaders the configuration options in `resolveLoader` are used.

In addition to that when trying to "resolve a module path" all module name variations in the configuration option `resolveLoader.moduleTemplates` are tried.

## Asynchronous

The above description suggests a serial process, but in the implementation the process is completely asynchronous and parallel. This may cause more filesystem access than required.