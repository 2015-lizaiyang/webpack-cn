_**webpack-dev-server**_ 是一个微型的 [Express](http://expressjs.com/) 服务器, 使用了_[webpack-dev-middleware][webpack-dev-middleware]_ 来为webpack bundle提供服务。同时也提供另一种方案，添加一个运行环境通过[Socket.IO](http://socket.io/)链接到服务器，服务器会推送关于编译状态的信息到对这些事件作出反应的客户端。你可以选择上述两种方案。取决于你的需要。好了我们来看看例子吧:

```javascript
var path = require("path");
module.exports = {
  entry: {
    app: ["./app/main.js"]
  },
  output: {
    path: path.resolve(__dirname, "build"),
    publicPath: "/assets/",
    filename: "bundle.js"
  }
};
```
_webpack_ 将会把app文件夹下面的main.js打包到bundle.js放在build文件夹里。


## 内容基础

_webpack-dev-server_将会以当前的文件目录作为服务目录作为内容基础，除非你自己指定内容基础。

```sh
$ webpack-dev-server --content-base build/
```
这样设置以后_webpack-dev-server_将build当作服务器的文件目录。它将监听文件的改动并且_bundle_将重新编译。这个被修改的_bundle_来自`publicPath`指定的相对目录下的内存（参看 [API](#api)）。他将不会写入你配置好的文件输出路径里。（默认）将会写入到内存里面的_bundle_里，_bundle_和配置的outputpath拥有相同的url 路径。


有了上面的设置下下面这个就可以访问了。
`localhost:8080/assets/bundle.js`
 
为了加载bundle文件, 你需要在`build`目录下面，创建一个`index.html`文件
例如:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

By default go to `localhost:8080/` to launch your app. For example with the configuration above (with publicPath) go to `localhost:8080/assets/`.

## 自动刷新

_webpack-dev-server_ 支持多个模式的自动刷新。

* Iframe 模式 (页面内嵌到iframe里面，等文件改动时候刷新)
* 行内模式 (在bundle文件里面添加一个小型webpack-dev-server客户端，当有改动触发刷新)
每个模式都支持热替换，当有一个改动的时候bundle将被通知，而不是整个页面的刷新。然后热替换执行环境加载更新过的moudle然后注入到运行的app里面。

### Iframe 模式
不需要改变配置. 只需要将浏览器打开 `http://<host>:<port>/webpack-dev-server/<path>`链接就可以. 比如.  `http://localhost:8080/webpack-dev-server/index.html`.

* 不需要改变配置.
* 很好的信息条展示在app的头部.
* app的url改变 **不** 影响浏览器的地址栏变化.

### Inline 模式
使用行内模式，你只需要在命令行加上`--inline` （不能在配置里面加）。这样webpack的配置里面就会加入webpack-dev-server的入口点。url请求方式没有改变还是`http://<host>:<port>/<path>`，比如`http://localhost:8080/index.html`。

* 需要命令行.
* 在浏览器端会有状态日志.
* app里面url改变会影响浏览器地址栏的url改变.


#### node.js API

webpack-dev-server里面没有`inline:true`标记，因为webpack-dev-server模块不能访问webpack配置。要在node api 里使用，就必须将webpack-dev-server客户端入口文件添加到webpack的配置里面。

想要这么做，只需要在所有的入口文件里面添加`webpack-dev-server/client?http://<path>:<port>/`就可以了。比如：


``` js
var config = require("./webpack.config.js");
config.entry.app.unshift("webpack-dev-server/client?http://localhost:8080/");
var compiler = webpack(config);
var server = new WebpackDevServer(compiler, {...});
server.listen(8080);
```


#### HTML
这同样是一种添加webpack-dev-server的方式：

```html
<script src="http://localhost:8080/webpack-dev-server.js"></script>
```


### 热替换
To enable Hot Module Replacement with the webpack-dev-server specify `--hot` on the command line. This adds the `HotModuleReplacementPlugin` to the webpack configuration.

The easiest way to use Hot Module Replacement with the webpack-dev-server is to use the inline mode.


#### CLI行内模式的热替换

Nothing more is needed. `--inline --hot` does all the relevant work automatically. The CLI of the webpack-dev-server automatically adds the special `webpack/hot/dev-server` entry point to your configuration.

Just navigate to `http://<host>:<port>/<path>` and let the magic happen.

You should see the following messages in the browser log:

``` text
[HMR] Waiting for update signal from WDS...
[WDS] Hot Module Replacement enabled.
```

Messages prefixed with `[HMR]` originate from the `webpack/hot/dev-server` module. Messages prefixed with `[WDS]` originate from the webpack-dev-server client.

It's important to specify a correct `output.publicPath` otherwise the hot update chunks cannot be loaded.


#### Hot Module Replacement with node.js API

Similar to the inline mode the user must make changes to the webpack configuration.

Three changes are needed:

* add an entry point to the webpack configuration: `webpack/hot/dev-server`.
* add the `new webpack.HotModuleReplacementPlugin()` to the webpack configuration.
* add `hot: true` to the webpack-dev-server configuration to enable HMR on the server.

I. e. with the above configuration:

``` js
var config = require("./webpack.config.js");
config.entry.app.unshift("webpack-dev-server/client?http://localhost:8080/", "webpack/hot/dev-server");
var compiler = webpack(config);
var server = new webpackDevServer(compiler, {
  hot: true
  ...
});
server.listen(8080);
```
### Working with editors/IDEs supporting "safe write"

Note that many editors support "safe write" feature and have it enabled by default, which makes dev server unable to watch files correctly. "Safe write" means changes are not written directly to original file but to temporary one instead, which is renamed and replaces original file when save operation is completed successfully. This behaviour causes file watcher to lose the track because the original file is removed. In order to prevent this issue, you have to disable "safe write" feature in your editor.

* **VIM** - set ":set backupcopy=yes" ([see documentation](http://vimdoc.sourceforge.net/htmldoc/options.html#'backupcopy'))
* **IntelliJ** - Settings -> System Settings -> Synchronization -> disable "safe write" (may differ in various IntelliJ IDEs, but you can still use the search feature)

## Proxy

The Webpack dev server makes use of [node-http-proxy](https://github.com/nodejitsu/node-http-proxy) to optionally proxy requests to a separate, possibly external, backend server. A sample configuration is below.

```javascript
proxy: {
  '/some/path*': {
    target: 'https://other-server.example.com',
    secure: false
  }
}
```

See the [node-http-proxy Options documentation](https://github.com/nodejitsu/node-http-proxy#options) for available configuration.

Proxying some URLs can be useful for a variety of configurations. One example is to serve JavaScript files and other static assets from the local development server but still send API requests to an external backend development server. Another example is splitting requests between two separate backend servers such as an authentication backend and a application backend.

### Bypass the Proxy

(Added in v1.13.0.) The proxy can be optionally bypassed based on the return from a function. The function can inspect the HTTP request, response, and any given proxy options. It must return either `false` or a URL path that will be served _instead_ of continuing to proxy the request.

For example, the configuration below will not proxy HTTP requests that originate from a browser. This is similar to the `historyApiFallback` option: browser requests will receive the HTML file as normal but API requests will be proxied to the backend server.

```javascript
proxy: {
  '/some/path*': {
    target: 'https://other-server.example.com',
    secure: false,
    bypass: function(req, res, proxyOptions) {
      if (req.headers.accept.indexOf('html') !== -1) {
        console.log('Skipping proxy for browser request.');
        return '/index.html';
    }
  }
}
```

### Rewriting URLs of proxy request

(Added in v???) The request to the proxy can be optionally rewritten by providing a function. The function can inspect and change the HTTP request.

For example, the configuration below will rewrite the HTTP requests to remove the part `/api` at the beginning of the URL.

```javascript
proxy: {
  '/api/*': {
    target: 'https://other-server.example.com',
    rewrite: function(req) {
      req.url = req.url.replace(/^\/api/, '');
    }
  }
}
```

## webpack-dev-server CLI

``` sh
$ webpack-dev-server <entry>
```

All _webpack_ [[CLI|cli]] options are valid for the _webpack-dev-server_ CLI too, but there is no `<output>` default argument. For the _webpack-dev-server_ CLI a `webpack.config.js` (or the file passed by the `--config` option) is accepted as well.

There are some additional options:

* `--content-base <file/directory/url/port>`: base path for the content.
* `--quiet`: don't output anything to the console.
* `--no-info`: suppress boring information.
* `--colors`: add some colors to the output.
* `--no-colors`: don't use colors in the output.
* `--compress`: use gzip compression.
* `--host <hostname/ip>`: hostname or IP. `0.0.0.0` binds to all hosts.
* `--port <number>`: port.
* `--inline`: embed the _webpack-dev-server_ runtime into the _bundle_.
* `--hot`: adds the `HotModuleReplacementPlugin` and switch the server to _hot mode_. Note: make sure you don't add `HotModuleReplacementPlugin` twice.
* `--hot --inline` also adds the `webpack/hot/dev-server` entry.
* `--lazy`: no watching, compiles on request (cannot be combined with `--hot`).
* `--https`: serves _webpack-dev-server_ over HTTPS Protocol. Includes a self-signed certificate that is used when serving the requests.
* `--cert`, `--cacert`, `--key`: Paths the certificate files.
* `--open`: opens the url in default browser (for webpack-dev-server versions > 2.0).
* `--history-api-fallback`: enables support for history API fallback.

### Additional configuration options

When using the CLI it's possible to have the webpack-dev-server options in the configuration file under the key `devServer`. Options passed via CLI arguments override options in configuration file. For options under `devServer` see next section.

Example

``` js
module.exports = {
  // ...
  devServer: {
    hot: true
  }
}
```

## API

``` javascript
var WebpackDevServer = require("webpack-dev-server");
var webpack = require("webpack");

var compiler = webpack({
  // configuration
});
var server = new WebpackDevServer(compiler, {
  // webpack-dev-server options

  contentBase: "/path/to/directory",
  // or: contentBase: "http://localhost/",

  hot: true,
  // Enable special support for Hot Module Replacement
  // Page is no longer updated, but a "webpackHotUpdate" message is send to the content
  // Use "webpack/hot/dev-server" as additional module in your entry point
  // Note: this does _not_ add the `HotModuleReplacementPlugin` like the CLI option does. 

  // Set this as true if you want to access dev server from arbitrary url.
  // This is handy if you are using a html5 router.
  historyApiFallback: false,

  // Set this if you want to enable gzip compression for assets
  compress: true,

  // Set this if you want webpack-dev-server to delegate a single path to an arbitrary server.
  // Use "*" to proxy all paths to the specified server.
  // This is useful if you want to get rid of 'http://localhost:8080/' in script[src],
  // and has many other use cases (see https://github.com/webpack/webpack-dev-server/pull/127 ).
  proxy: {
    "*": "http://localhost:9090"
  },

  // pass [static options](http://expressjs.com/en/4x/api.html#express.static) to inner express server
  staticOptions: {
  },

  // webpack-dev-middleware options
  quiet: false,
  noInfo: false,
  lazy: true,
  filename: "bundle.js",
  watchOptions: {
    aggregateTimeout: 300,
    poll: 1000
  },
  publicPath: "/assets/",
  headers: { "X-Custom-Header": "yes" },
  stats: { colors: true }
});
server.listen(8080, "localhost", function() {});
// server.close();
```

See _[[webpack-dev-middleware]]_ for documentation on middleware options.

Notice that _webpack configuration_ is not passed to `WebpackDevServer` API, thus `devServer` option in webpack configuration is not used in this case. Also, there is no _inline mode_ for `WebpackDevServer` API. `<script src="http://localhost:8080/webpack-dev-server.js"></script>` should be inserted to HTML page manually.

### The `historyApiFallback` option

If you are using the HTML5 history API you probably need to serve your `index.html` in place of 404 responses, which can be done by setting `historyApiFallback: true`. However, if you have modified `output.publicPath` in your Webpack configuration, you need to specify the URL to redirect to. This is done using the `historyApiFallback.index` option:

    // output.publicPath: '/foo-app/'
    historyApiFallback: {
      index: '/foo-app/'
    }


## Combining with an existing server

You may want to run a backend server or a mock of it in development. You should **not** use the _webpack-dev-server_ as a backend. Its only purpose is to serve static (webpacked) assets.

You can run two servers side-by-side: The _webpack-dev-server_ and your backend server.

In this case you need to teach the webpack-generated assets to make requests to the _webpack-dev-server_ even when running on a HTML-page sent by the backend server. On the other side you need to teach your backend server to generate HTML pages that include `script` tags that point to assets on the _webpack-dev-server_. In addition to that you need a connection between the _webpack-dev-server_ and the _webpack-dev-server_ runtime to trigger reloads on recompilation.

To teach _webpack_ to make requests (for chunk loading or HMR) to the _webpack-dev-server_ you need to provide **a full URL in the `output.publicPath`** option.

To make a connection between _webpack-dev-server_ and its runtime best, use the _inline mode_ with `--inline`. The _webpack-dev-server_ CLI automatically includes an entry point which establishes a WebSocket connection. (You can also use the _iframe_ mode if you point `--content-base` of the _webpack-dev-server_ to your backend server.  **If you need a websocket connection to your backend server**, you'll have to use iframe mode.

When you use the _inline mode_ just open the backend server URL in your web browsers. (If you use the _iframe mode_ open the `/webpack-dev-server/` prefixed URL of the _webpack-dev-server_.)

Summary and example:

* _webpack-dev-server_ on port `8080`.
* backend server on port `9090`.
* generate HTML pages with `<script src="http://localhost:8080/assets/bundle.js">`.
* webpack configuration with `output.publicPath = "http://localhost:8080/assets/"`.
* when compiling files for production, use `--output-public-path /assets/`.
* _inline mode_:
  * `--inline`.
  * open `http://localhost:9090`.
* or _iframe mode_:
  * _webpack-dev-server_ `contentBase = "http://localhost:9090/"` (`--content-base`).
  * open `http://localhost:8080/webpack-dev-server/`.

Or use the proxy option...

[webpack-dev-middleware]:docs/webpack-dev-middleware.md