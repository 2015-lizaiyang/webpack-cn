> Note: The webpack-dev-middleware is for advanced users. See [[webpack-dev-server]] for a ready-to-use solution.

The [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) is a small middleware for a connect-based middleware stack. It uses webpack to compile assets in-memory and serve them.  When a compilation is running every request to the served webpack assets is blocked until we have a stable bundle.

> Now, [koa-webpack-middleware](https://github.com/leecade/koa-webpack-middleware) ready for [koa2](https://github.com/koajs/koa/tree/v2.x) ecosystem also with HMR supports.

You can use it in two modes:

* watch mode (default): The compiler recompiles on file change.
* lazy mode: The compiler compiles on every request to the entry point.

## API

``` javascript
var webpackDevMiddleware = require("webpack-dev-middleware");
var webpack = require("webpack");

var compiler = webpack({
	// configuration
	output: { path: '/' }
});

app.use(webpackDevMiddleware(compiler, {
	// options
}));
```

### options

#### `noInfo`

Display no info to console (only warnings and errors)

Default: `false`

#### `quiet`

Display nothing to the console

Default: `false`

#### `lazy`

Switch into lazy mode.

Default: `false`

#### `filename`

In lazy mode: Switch request should trigger the compilation.

In most cases this equals the webpack configuration option `output.filename`.

#### `watchOptions.aggregateTimeout`

Delay the rebuilt after the first change. Value is a time in ms.

Default: `300`

#### `watchOptions.poll`

`true`: use polling

number: use polling with specified interval

Default: `undefined`

#### `publicPath` (**required**)

The path where to bind the middleware to the server.

In most cases this equals the webpack configuration option `output.publicPath`.

#### `headers`

Add custom headers. i. e. `{ "X-Custom-Header": "yes" }`

#### `stats`

Output options for the stats. See [[node.js API]].

### `middleware.invalidate()`

Manually invalidate the compilation. Useful if stuff of the compiler has changed.

### `middleware.fileSystem`

A readable (in-memory) filesystem that can access the compiled data.