CommonJS 是以在浏览器环境之外构建 JavaScript 生态系统为目标而产生的项目，比如在服务器和桌面环境中。
这个项目最开始是由 Mozilla 的工程师 Kevin Dangoor 在2009年1月创建的，当时的名字是 ServerJS。
>我在这里描述的并不是一个技术问题，而是一件重大的事情，让大家走到一起来做决定，迈出第一步，来建立一个更大更酷的东西。 —— Kevin Dangoor's What Server Side JavaScript needs

CommonJS 规范是为了解决 JavaScript 的作用域问题而定义的模块形式，可以使每个模块它自身的命名空间中执行。该规范的主要内容是，模块必须通过 module.exports 导出对外的变量或接口，通过 require() 来导入其他模块的输出到当前模块作用域中。

Commonjs 提供两个工具:

1.  `require()` 函数,当前作用域下导入其他模块.
2. `module` 对象, 当前域到处变量和接口.


## 无格式的js例子

这是一个没有commonjs的例子：

在`salute.js`文件里面定义一个别的地方要用到的变量MySalute。

``` javascript
// salute.js
var MySalute = "Hello";
```

在`world.js`文件里面使用`salute.js`里面定义的变量
``` javascript	
// world.js
var Result = MySalute + " world!";
```

## Module 定义
可以看到`world.js` 里面的`MySalute` is not defined。
所以，我们需要给每个文件定义一个module：

``` javascript
// salute.js
var MySalute = "Hello";
module.exports = MySalute;
```

``` javascript
// world.js
var Result = MySalute + "world!";
module.exports = Result;
```

我们用了一个特殊变量`module`，并将定义的变量引用放到`module.exports` 以至于CommonJS模块系统知道我们模块的对象。
`salute.js` 暴露了 `MySalute`, `world.js` 暴露了 `Result`.

## Module 依赖
我们还差一步就完成了：依赖定义。
我们给每个文件定义一个独立的模块，但是`world.js`得知道是谁定义的`Mysalute`
We've already defined every script as an independent module, but `world.js`

``` javascript
// salute.js
var MySalute = "Hello";
module.exports = MySalute;
```

``` javascript
// world.js
var MySalute = require("./salute");
var Result = MySalute + "world!";
module.exports = Result;
```

注一道我们没有用完整的文件名`salute.js` 而是`./salute` ， `./` 表示和`world` module在同一个目录.


## 例子

### 函数

``` javascript
// moduleA.js
module.exports = function( value ){
	return value*2;
}
```

``` javascript
// moduleB.js
var multiplyBy2 = require('./moduleA');
var result = multiplyBy2( 4 );
```

