AMD（异步模块定义）是为浏览器环境设计的，因为 CommonJS 模块系统是同步加载的，当前浏览器环境还没有准备好同步加载模块的条件。

AMD 定义了一套 JavaScript 模块依赖异步加载标准，来解决同步加载的问题。

## Specification

Modules are defined using the `define` function.

### `define`

The define function is how modules are defined with AMD. It is just a function that has this signature

``` javascript
define(id?: String, dependencies?: String[], factory: Function|Object);
```

#### `id`

Specifies the module name. It is optional.

#### `dependencies`

This argument specifies which module dependencies the module being defined has.
It is an array containing module identifiers.
It is optional, but if omitted, it defaults to ["require", "exports", "module"].

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