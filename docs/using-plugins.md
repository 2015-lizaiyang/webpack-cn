插件可以完成更多 loader 不能完成的功能。




接下来，我们利用一个最简单的 BannerPlugin 内置插件来实践插件的配置和运行，这个插件的作用是给输出的文件头部添加注释信息。

修改 webpack.config.js，添加 plugins：

Use plugins to add functionality typically related to bundles in webpack.  For example, the [BellOnBundlerErrorPlugin](https://github.com/senotrusov/bell-on-bundler-error-plugin) will notify you of an error in the bundler build process.  

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

当通过npm安装第三方插件时候建议用这个工具:
[webpack-load-plugins)](https://www.npmjs.com/package/webpack-load-plugin)
它会检查你的依赖里面的所有第三方插件并懒加载之

## See also

* [[list of plugins]]