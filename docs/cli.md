## 安装

``` sh
$ npm install webpack -g
```

这样`webpack`就全局可用了.




## 纯 CLI

``` sh
webpack <entry> <output>
```



### `entry`

传入一个文件或者一个请求串。也可以传入多个入口文件（每个入口文件都将在加载时候执行）.

如果传一个类格式为`<name>=<request>`的值，你就可以创建额外的entry.

这个entry将会被映射到配置项`entry`里。



### `output`

 传入一个文件路径.

与配置里面的 `output.path` 和 `output.filename`对应.



### Configuration options

Many configuration options are mapped from CLI options. I. e. `--debug` maps to `debug: true`, or `--output-library-target` to `output.libraryTarget`.

You see a list of all options, if you don't pass any option.



### Plugins

Some plugins are mapped to CLI options. I. e. `--define <string>=<string>` maps to the `DefinePlugin`.

You see a list of all options, if you don't pass any option.



### Development shortcut `-d`

Equals to `--debug` `--devtool source-map` `--output-pathinfo`



### Production shortcut `-p`

Equals to `--optimize-minimize` `--optimize-occurence-order`



### Watch mode `--watch`

Watches all dependencies and recompile on change.


### Configuration file `--config example.config.js`

Specifies a different configuration file to pick up. Use this if you want to specify something different than `webpack.config.js`, which is the default.


### Display options

#### `--progress`

Display a compilation progress to stderr.

#### `--json`

Write JSON to stdout instead of a human readable format.

> Hint: Try to put the result into the [analyse tool](http://webpack.github.com/analyse).

#### `--no-color`

Disable colors to display the statistics.

#### `--sort-modules-by`, `--sort-chunks-by`, `--sort-assets-by`

Sort the modules/chunks/assets list by a column.

#### `--display-chunks`

Display the separation of the modules into chunks.

#### `--display-reasons`

Show more information about the reasons why a module is included.

#### `--display-error-details`

Show more information about the errors. I. e. this shows which paths are tried while resolving a module.

#### `--display-modules`

Show hidden modules. Modules are hidden from output by default when they live inside directories called `["node_modules", "bower_components", "jam", "components"]`

### Profiling

If you wish to have a more in-depth idea of what is taking how long, you can use the `--profile` switch. This will cause WebPack to display more detailed timing informations. Combine this with the switches above to get a very detailed message and information set, which will contain the timings of your modules.

#### The timing "keys"

- `factory`: The time it took to build the module information.
- `building`: The time that was spent building the module (loaders, for example).
- `dependencies`: The time that was spent gathering and connecting the dependencies.



### Additional configuration options

When using the CLI it's possible to have the following options in the configuration file. They passed in other ways when using the node.js API.




#### `watch`

Enter watch mode, which rebuilds on file change.

#### `watchOptions.aggregateTimeout`

Delay the rebuilt after the first change. Value is a time in ms.

> Default: 300

#### `watchOptions.poll`

`true`: use polling

number: use polling with specified interval

> Default: `undefined` 

#### `stats`

Display options. See [[node.js API]] `Stats.toString()` for more details.