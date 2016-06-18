有一个使用webpack 以及 [webpack-dev-server](http://webpack.github.io/docs/webpack-dev-server.html) 的Grunt插件: [grunt-webpack](https://github.com/webpack/grunt-webpack).

它用起来非常方便:

``` javascript
module.exports = function(grunt) {
	grunt.loadNpmTasks("grunt-webpack");
	grunt.initConfig({
		webpack: {
			options: {
				// configuration for all builds
			},
			build: {
				// configuration for this build
			}
		},
		"webpack-dev-server": {
			options: {
				webpack: {
					// configuration for all builds
				},
				// server and middleware options for all builds
			},
			start: {
				webpack: {
					// configuration for this build
				},
				// server and middleware options for this build
			}
		}
	});
};
```

## 开发

开发过程中的最佳利器就是 [[webpack-dev-server]], 但是它需要生成一个server. 如果没法做到这个或者太复杂没法做到, 正常的
build - watch cycle 也是可行的.

## 示例

阅读一下这个Gruntfile例子.它包含了以下三个模式:

* webpack-dev-server
* build - watch cycle
* production build

[Example Gruntfile](https://github.com/webpack/webpack-with-common-libs/blob/master/Gruntfile.js)
