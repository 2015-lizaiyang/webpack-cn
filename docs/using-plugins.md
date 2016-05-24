插件可以完成更多 loader 不能完成的功能。
Use plugins to add functionality typically related to bundles in webpack.  
比如 [BellOnBundlerErrorPlugin](https://github.com/senotrusov/bell-on-bundler-error-plugin) 可以在build的过程中给出错误提示.  

## 内置 plugins

插件的使用一般是在 webpack 的配置信息 plugins 选项中指定。

``` javascript
// webpack should be in the node_modules directory, install if not.
var webpack = require("webpack");

module.exports = {
	plugins: [
		new webpack.ResolverPlugin([
			new webpack.ResolverPlugin.DirectoryDescriptionFilePlugin("bower.json", ["main"])
		], ["normal", "loader"])
	]
};
```

## 其它 plugins


Webpack 本身内置了一些常用的插件，还可以通过 npm 安装第三方插件。:

``` text
npm install component-webpack-plugin
```

可以这么使用:

``` javascript
var ComponentPlugin = require("component-webpack-plugin");
module.exports = {
	plugins: [
		new ComponentPlugin()
	]
}
```

当通过npm安装第三方插件时候建议用这个工具:[webpack-load-plugins](https://www.npmjs.com/package/webpack-load-plugin)
它会检查你的依赖里面的所有第三方插件并懒加载之.

## See also

* [plugin列表][list of plugins]

[list of plugins]: list-of-plugins.md