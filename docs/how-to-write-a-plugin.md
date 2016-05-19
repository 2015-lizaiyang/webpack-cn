插件为第三方开发者释放了Webpack的最大可能性。利用多级回调开发者可以把他们自己的需要的功能引入到Webpack里面来。Build插件比Build loader 更进一步。因为你需要理解Webpack底层的东西。要有月底源代码的准备。

## Compiler 和 Compilation
开发插件最重要的两个资源就是 `compiler` 和 `compilation` 对象，理解他们的是扩展Webpack重要的一步
- `compiler`对象包涵了Webpack环境所有的的配置信息，这个对象在Webpack启动时候被构建，并配置上所有的设置选项包括 options，loaders，plugins。当启用一个插件到Webpack环境的时候，这个插件就会接受一个指向compiler的参数。运用这个参数来获取到Webpack环境
- `compilation`代表了一个单一构建版本的物料。在webpack中间件运行时，每当一个文件发生改变时就会产生一个新的compilation从而产生一个新的变异后的物料集合。compilation列出了很多关于当前模块资源的信息，编译后的资源信息，改动过的文件，以及监听过的依赖。compilation也提供了插件需要自定义功能的回调点。

这两个组件在所有的Webpack插件中都是不可分割的一部分（特别是`compilation`），所以对于开发者来说熟悉这两个组件的源文件将是你受益很多：

- [Compiler 源文件](https://github.com/webpack/webpack/blob/master/lib/Compiler.js)
- [Compilation 源文件](https://github.com/webpack/webpack/blob/master/lib/Compilation.js)

## 插件基本结构

Plugins are instanceable objects with an `apply` method on their prototype. This `apply` method is called once by the Webpack compiler while installing the plugin. The `apply` method is given a reference to the underlying Webpack compiler, which grants access to compiler callbacks. A simple plugin is structured as follows:

```javascript
function HelloWorldPlugin(options) {
  // Setup the plugin instance with options...
}

HelloWorldPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function() {
    console.log('Hello World!'); 
  });
};

module.exports = HelloWorldPlugin;
```

Then to install the plugin, just include an instance in your Webpack config `plugins` array:

```javascript
var HelloWorldPlugin = require('hello-world');

var webpackConfig = {
  // ... config settings here ...
  plugins: [
    new HelloWorldPlugin({options: true})
  ]
};
```

## 访问 compilation

Using the compiler object, you may bind callbacks that provide a reference to each new compilation. These compilations provide callbacks for hooking into numerous steps within the build process.

```javascript
function HelloCompilationPlugin(options) {}

HelloCompilationPlugin.prototype.apply = function(compiler) {

  // Setup callback for accessing a compilation:
  compiler.plugin("compilation", function(compilation) {
    
    // Now setup callbacks for accessing compilation steps:
    compilation.plugin("optimize", function() {
      console.log("Assets are being optimized.");
    });
  });
});

module.exports = HelloCompilationPlugin;
```

For more information on what callbacks are available on the `compiler`, `compilation`, and other important objects, see the [[plugins API|plugins]] doc.

## 异步编译插件

Some compilation plugin steps are asynchronous, and pass a callback function that _must_ be invoked when your plugin is finished running.

```javascript
function HelloAsyncPlugin(options) {}

HelloAsyncPlugin.prototype.apply = function(compiler) {
  compiler.plugin("emit", function(compilation, callback) {

    // Do something async...
    setTimeout(function() {
      console.log("Done with async work...");
      callback();
    }, 1000);

  });
});

module.exports = HelloAsyncPlugin;
```

## 例子

Once we can latch onto the Webpack compiler and each individual compilations, the possibilities become endless for what we can do with the engine itself. We can reformat existing files, create derivative files, or fabricate entirely new assets.

Let's write a simple example plugin that generates a new build file called `filelist.md`; the contents of which will list all of the asset files in our build. This plugin might look something like this:

```javascript
function FileListPlugin(options) {}

FileListPlugin.prototype.apply = function(compiler) {
  compiler.plugin('emit', function(compilation, callback) {
    // Create a header string for the generated file:
    var filelist = 'In this build:\n\n';

    // Loop through all compiled assets,
    // adding a new line item for each filename.
    for (var filename in compilation.assets) {
      filelist += ('- '+ filename +'\n');
    }
    
    // Insert this list into the Webpack build as a new file asset:
    compilation.assets['filelist.md'] = {
      source: function() {
        return filelist;
      },
      size: function() {
        return filelist.length;
      }
    };

    callback();
  });
};

module.exports = FileListPlugin;
```