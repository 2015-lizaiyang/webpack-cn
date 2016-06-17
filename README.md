## webpack doc
中文翻译
![[ds](stephenzhao.github.io/webpack_cn_doc/)](QQ20160303-0.png)


# Summary

* [关于文档](README.md)
* [概览](docs/Home.md)
* 开始
   * [动机](docs/motivation.md)
   * [webpack是什么](docs/what-is-webpack.md)
   * [安装](docs/installation.md)
   * [用法](docs/usage.md)
   * Require Modules
   * Vendor Modules
   * [使用 Loaders](docs/using-loaders.md)
   * [使用 Plugins](docs/using-plugins.md)
   * [工具](docs/dev-tools.md)
   * [故障处理](docs/troubleshooting.md)
* 教程与例子
   * [开始](http:/webpack.github.io/docs/tutorials/docs/getting-started.md)
   * [教程列表](docs/list-of-tutorials.md)
   * [示例](docs/examples.md)
* 指南
   * [CommonJs](docs/commonjs.md)
   * [AMD](docs/amd.md)
   * [webpack for browserify users](docs/webpack-for-browserify-users.md)
   * [代码拆分](docs/code-splitting.md)
   * [Stylesheets](docs/stylesheets.md)
   * [性能优化](docs/optimization.md)
   * [长期缓存](docs/long-term-caching.md)
   * [如何编写loader](docs/how-to-write-a-loader.md)
   * [如何编写插件](docs/how-to-write-a-plugin.md)
   * [Multiple entry points](docs/multiple-entry-points.md)
   * [Library and externals](docs/library-and-externals.md)
   * [Shimming modules](docs/shimming-modules.md)
   * [Testing](docs/testing.md)
   * [构建性能](docs/build-performance.md)
   * [热替换](docs/hot-module-replacement-with-webpack.md)
   * [Comparison](docs/comparison.md)
* webpack with
   * [grunt | 使用grunt](docs/usage-with-grunt.md)
   * [gulp | 使用gulp](docs/usage-with-gulp.md)
   * [bower | Usage with bower](docs/usage-with-bower.md)
   * [karma | Usage with karma](docs/usage-with-karma.md)
* Lists
   * [loader conventions](docs/loader-conventions.md)
   * [List of loaders](docs/list-of-loaders.md)
   * [List of plugins](docs/list-of-plugins.md)
   * [List of hints](docs/list-of-hints.md)
   * [Configuration](docs/configuration.md)
   * [CLI](docs/cli.md)
   * [Node.js API](docs/node.js-api.md)
   * [API in modules](docs/api-in-modules.md)
   * [Loaders](docs/loaders.md)
   * [Plugins](docs/plugins.md)
   * [Context](docs/context.md)
   * [Resolving](docs/resolving.md)
   * [Hot Module Replacement](docs/hot-module-replacement.md)
   * Dev Tools
       * [webpack-dev-server](docs/webpack-dev-server.md)
       * [webpack-dev-middleware](docs/webpack-dev-middleware.md)
* Development
   * [Changelog](docs/changelog.md)
   * [Roadmap](docs/roadmap.md)
   * [Ideas](docs/ideas.md)
   * Contributing

## how to work cooperatively
1. 安装[gitbook](https://github.com/GitbookIO/gitbook) 客户端

```zsh
npm install gitbook-cli -g
```
2.clone [我的书的代码](https://github.com/stephenzhao/webpack_cn_doc)

```zsh
git clone https://github.com/stephenzhao/webpack_cn_doc

```
3.利用任意一种你觉得顺手的编辑器比如
![markdown](QQ20160303-0@2x.png) 来编辑章节
推荐使用[Gitbook Editor](https://www.gitbook.com/editor/osx)

4.保存发布，发pr给我就好了

``` js
//创建预览页面 http://localhost:4000
$ gitbook serve
Press CTRL+C to quit ...

Live reload server started on port: 35729
Starting build ...
Successfully built!

Starting server ...
Serving book on http://localhost:4000

```


## gitbook 如何工作的


这片[文章](http://www.chengweiyang.cn/gitbook/introduction/README.html)或许可以给你答案


