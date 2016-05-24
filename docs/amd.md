AMD（异步模块定义）是为浏览器环境设计的，因为 CommonJS 模块系统是同步加载的，当前浏览器环境还没有准备好同步加载模块的条件。

AMD 定义了一套 JavaScript 模块依赖异步加载标准，来解决同步加载的问题。

## 定义

使用`define`函数定义Module.

### `define`

格式如下：

``` javascript
define(id?: String, dependencies?: String[], factory: Function|Object);
```

#### `id`

module的名字，可选的.

#### `dependencies`

dependencies 指定了所要依赖的module列表，它是一个数组，也是可选的参数，每个依赖的模块的输出将作为参数一次传入 factory 中。如果没有指定 dependencies，那么它的默认值是 ["require", "exports", "module"]。

#### `factory`
factory 是最后一个参数，它包裹了模块的具体实现，它是一个函数（只能调用一次）或者对象。
如果是函数，那么它的返回值就是模块的输出接口或值。

## 例子


### 命名 module

定义一个名为 myModule 的模块，它依赖 jQuery 模块：

```javascript
define('myModule', ['jquery'], function($) {
	// $ is the export of the jquery module.
	$('body').text('hello world');
});
// and use it
require(['myModule'], function(myModule) {});
```

注意：在 webpack 中，模块名只有局部作用域，在 Require.js 中模块名是全局作用域，可以在全局引用。



### 匿名 module

定义一个没有 id 值的匿名模块，通常作为应用的启动函数：

```javascript
define(['jquery'], function($) {
	$('body').text('hello world');
});
```

### 多个模块依赖

```javascript
define(['jquery', './math.js'], function($, math) {
	// $ and math are the exports of the jquery module.
	$('body').text('hello world');
});
```

### 模块输出

输出模块自身.

```javascript
define(['jquery'], function($) {

	var HelloWorldize = function(selector){
		$(selector).text('hello world');
	};

	return HelloWorldize;
});
```

### 在模块定义内部引用依赖

```javascript
define(function(require) {
	var $ = require('jquery');
	$('body').text('hello world');
});
```