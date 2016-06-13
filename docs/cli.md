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

显示module分离成chunk的信息.

#### `--display-reasons`

显示更多关于一个模块为什么会被包含的原因。

#### `--display-error-details`

显示更多的错误信息. 比如. 展示在解析当前这个模块的时候尝试解析了哪些路径。

#### `--display-modules`

展示隐藏的module.Module信息在输出的时候默认是被隐藏的当她们在 `["node_modules", "bower_components", "jam", "components"]`这些目录下面的时候。

### 性能分析

如果你希望油更深入的了解什么消耗了多久时间，你可以使用`--peofile`开关。这将会让webpack显示详细的小号的时间信息。结合这个你将会得到非常详细的信息集合，包括你每个module编译的时间。

#### 计时 "keys"

- `factory`: build模块信息花费的时间.
- `building`: build模块所花费的时间（比如loader模块）.
- `dependencies`: 收集和处理依赖的时间.



### 附加的配置参数

当使用CLI的时候，在配置文件里面可以带有下面这些参数的。在使用node.js API时他们将以别的方式传入.




#### `watch`

进入watch模式.

#### `watchOptions.aggregateTimeout`

当文件修改时，延迟重编译时间，单位时ms.

> 默认: 300

#### `watchOptions.poll`

`true`:使用轮询

number: 使用指定次数的轮询

> 默认: `undefined` 

#### `stats`

显示参数. 可参见 [node.js API][nodejs] `Stats.toString()` .

[nodejs]:docs/node.js-api.md