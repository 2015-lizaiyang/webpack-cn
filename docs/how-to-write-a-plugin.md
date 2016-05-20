插件为第三方开发者释放了Webpack的最大可能性。利用多级回调开发者可以把他们自己的需要的功能引入到Webpack里面来。Build插件比Build loader 更进一步。因为你需要理解Webpack底层的东西。要有月底源代码的准备。

## Compiler 和 Compilation
开发插件最重要的两个资源就是 `compiler` 和 `compilation` 对象，理解他们的是扩展Webpack重要的一步
- `compiler`对象包涵了Webpack环境所有的的配置信息，这个对象在Webpack启动时候被构建，并配置上所有的设置选项包括 options，loaders，plugins。当启用一个插件到Webpack环境的时候，这个插件就会接受一个指向compiler的参数。运用这个参数来获取到Webpack环境
- `compilation`代表了一个单一构建版本的物料。在webpack中间件运行时，每当一个文件发生改变时就会产生一个新的compilation从而产生一个新的变异后的物料集合。compilation列出了很多关于当前模块资源的信息，编译后的资源信息，改动过的文件，以及监听过的依赖。compilation也提供了插件需要自定义功能的回调点。

这两个组件在所有的Webpack插件中都是不可分割的一部分（特别是`compilation`），所以对于开发者来说熟悉这两个组件的源文件将是你受益很多：

- [Compiler 源文件](https://github.com/webpack/webpack/blob/master/lib/Compiler.js)
- [Compilation 源文件](https://github.com/webpack/webpack/blob/master/lib/Compilation.js)

## 插件基本结构

Plugins是可以用自身原型方法`apply`来实例化的对象。`apply`只在安装插件被Webpack compiler执行一次。`apply`方法传入一个Webpck compiler的引用，来访问编译器回调。

一个简单的插件结构：

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

安装插件时, 只需要将它的一个实例放到 Webpack config `plugins` 数组里面:

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

使用compiler对象，你可能需要绑定带有各个新compilation的引用的回调函数。这些compilation提供回调函数连接成许多构建过程中的步骤。

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

更多关于在`compiler`, `compilation`等对象中哪些回调有用，看一下
[plugins API](http://stephenzhao.github.io/webpack-cn/docs/plugins.html)

## 异步编译插件
有些compilation插件的步骤时异步的，并且会传入一个当你的插件运行完成时候_必须_调用的回调函数。

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

我们了解了Webpack compiler和各个compilations，我们就可以用它们来创造无尽的可能。我们可以重定当前文件的格式，生成一个衍生文件，或者制造出一个全新的assets

下面我们将写一个简单的插件，生成一个`filelist.md`文件，里面的内容是，列出我们build的所有asset 文件。

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