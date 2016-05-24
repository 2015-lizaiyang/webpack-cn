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

dependencies 指定了所要依赖的模块列表，它是一个数组，也是可选的参数，每个依赖的模块的输出将作为参数一次传入 factory 中。如果没有指定 dependencies，那么它的默认值是 ["require", "exports", "module"]。

#### `factory`

The last argument is the one who defines the module. It can be a function (which should be called once), or an object.
If the factory is a function, the value returned will be the exported value for the module.

## Examples

Let's see some examples:

### Named module

Defines a module named `myModule` that requires `jQuery`.

```javascript
define('myModule', ['jquery'], function($) {
	// $ is the export of the jquery module.
	$('body').text('hello world');
});
// and use it
require(['myModule'], function(myModule) {});
```

Note: In webpack a named module is only locally available. In Require.js a named module is globally available.

### Anonymous module

Define a module without specifying its id.

```javascript
define(['jquery'], function($) {
	$('body').text('hello world');
});
```

### Multiple dependencies

Define a module with multiple dependencies. Note that each dependency export will be passed to the factory function.

```javascript
define(['jquery', './math.js'], function($, math) {
	// $ and math are the exports of the jquery module.
	$('body').text('hello world');
});
```

### Export value

Define a module that exports itself.

```javascript
define(['jquery'], function($) {

	var HelloWorldize = function(selector){
		$(selector).text('hello world');
	};

	return HelloWorldize;
});
```

### Using require to load dependencies

```javascript
define(function(require) {
	var $ = require('jquery');
	$('body').text('hello world');
});
```