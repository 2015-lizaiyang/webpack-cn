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

As it is, `world.js` will not work as `MySalute` is not defined.
We need to define each script as a module:  

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

Here we make use of the special object `module` and place a reference of our
variable into `module.exports` so the CommonJS module system knows this is 
the object of our module we want to show to the world.
`salute.js` discloses `MySalute`, and `world.js` discloses `Result`.

## Module dependency

We're near but there's still a step missing: dependency definition.
We've already defined every script as an independent module, but `world.js`
still needs to know who defines `MySalute`:

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

Note that we didn't use the full filename `salute.js` but `./salute` when calling 
`require`, so you can omit the extension of your scripts. `./` means that the `salute` module is in the same directory as the `world` module.


## Examples

### Functions

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

