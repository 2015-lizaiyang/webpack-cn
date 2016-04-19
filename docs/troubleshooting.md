# resolving

## general resolving issues

* `--display-error-details` give you more details.
* Read [[Configuration]] regarding resolving starting at `resolve`
  * loaders have their own resolving configuration `resolveLoader`

## `npm link`ed modules don't find their dependencies

The node.js module resolving algorithm is pretty simple: module dependencies are looked up in `node_modules` folders in every parent directory of the requiring module. When you `npm link` modules with peer dependencies that are not in your root directory, modules can no longer be found. (You probably want to consider `peerDependencies` with `npm link` as broken by design in node.js.) Note that a dependency to the application (even if this is not the perfect design) is also a kind of peerDependency even if it's not listed as such in the module's `package.json`.

But you can easily workaround that in webpack: Add the `node_modules` folder of your application to the resolve paths. There are two config options for this: `resolve.fallback` and `resolveLoader.fallback`.

Here is a config example:

``` js
module.exports = {
  resolve: { fallback: path.join(__dirname, "node_modules") },
  resolveLoader: { fallback: path.join(__dirname, "node_modules") }
};
```

# Watching

## webpack doesn't recompile on change while watching

### Not enough watchers

Verify that if you have enough available watchers in your system. If this value is too low, the file watcher in Webpack won't recognize the changes:

```
cat /proc/sys/fs/inotify/max_user_watches
```

Arch users, add `fs.inotify.max_user_watches=524288` to `/etc/sysctl.d/99-sysctl.conf` and then execute `sysctl --system`. Ubuntu users (and possibly others): `echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p`.

### OS-X fsevents bug

On OS-X folders can get corrupted. See this article:

[OS X FSEvents bug may prevent monitoring of certain folders](http://feedback.livereload.com/knowledgebase/articles/86239-os-x-fsevents-bug-may-prevent-monitoring-of-certai)

### Windows paths

webpack expects absolute paths for many config options. `__dirname + "/app/folder"` is wrong, because windows uses `\` as path separator. This breaks some stuff.

Use the correct separators. I.e. `path.resolve(__dirname, "app/folder")` or `path.join(__dirname, "app", "folder")`.

### Vim

On some machines Vim is preconfigured with the [backupcopy option](http://vimdoc.sourceforge.net/htmldoc/options.html#'backupcopy') set to **auto**. This could potentially cause problems with the system's file watching mechanism. Switching this option to `yes` will make sure a copy of the file is made and the original one overwritten on save. 

`:set backupcopy=yes`