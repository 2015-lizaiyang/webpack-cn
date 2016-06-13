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



### 配置选项

许多配置项都和 CLI 的参数项对应. 比如. `--debug` 对应 `debug: true`, 以及 `--output-library-target` 对应 `output.libraryTarget`.

如果不穿任何参数，则会显示webpack的帮助信息，里面有非常多的参数可用.



### 插件

一些插件也和CLI的参数对应. `--define <string>=<string>` 对应 `DefinePlugin`.

如果不穿任何参数，则会显示webpack的帮助信息，里面有非常多的参数可用.



### 开发环境 缩写 `-d`

等于 `--debug` `--devtool source-map` `--output-pathinfo`



###生产环境缩写 `-p`

等于 `--optimize-minimize` `--optimize-occurence-order`



### 监听模式 `--watch`

在文件改动监听所有的依赖以及重编译。


### 配置文件 `--config example.config.js`

挂载一个指定的config文件. 如果你想使用 `webpack.config.js`之外的文件作为配置文件,就使用这个方法。webpack 默认使用`webpack.config.js`做配置文件.


### 显示参数

#### `--progress`

在标准输入输出端口显示一次编译的进度。

#### `--json`
显示JSON格式的输出而不是，易读的格式。


> 提示: 当需要将数据放倒 [analyse tool](http://webpack.github.com/analyse)的时候可以使用.

#### `--no-color`

显示信息无颜色.

#### `--sort-modules-by`, `--sort-chunks-by`, `--sort-assets-by`

将 modules/chunks/assets 列表纵列排序.

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