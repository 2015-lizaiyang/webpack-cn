## webpack doc
中文翻译
![[ds](stephenzhao.github.io/webpack_cn_doc/)](QQ20160303-0.png)

# Summary

* [关于文档](README.md)
* [概览](docs/home.md)
* 开始
  * [动机][Motivation]
  * [webpack是什么][What is webpack? | What is webpack]
  * [安装][Installation]
  * [用法][Usage]
  * Require Modules
  * Vendor Modules
  * [Using Loaders]
  * [Using Plugins]
  * [Dev Tools]
  * [Troubleshooting]
* Tutorials and examples
  * [Getting started](http://webpack.github.io/docs/tutorials/getting-started/)
  * [List of tutorials]
  * [examples]
* Guides
  * [CommonJs]
  * [AMD]
  * [webpack for browserify users]
  * [代码拆分][Code Splitting]
  * [Stylesheets]
  * [性能优化][Optimization]
  * [Long-term Caching]
  * [如何编写loader][How to write a loader]
  * [How to write a plugin]
  * [Multiple entry points]
  * [Library and externals]
  * [Shimming modules]
  * [Testing]
  * [Build performance]
  * [Hot Module Replacement with webpack]
  * [Comparison]
* webpack with
  * [grunt | Usage with grunt]
  * [使用gulp][gulp | Usage with gulp]
  * [bower | Usage with bower]
  * [karma | Usage with karma]
* Lists
  * [loader conventions]
  * [List of loaders]
  * [List of plugins]
  * [List of tutorials]
  * [List of hints]
* API
  * [Configuration]
  * [CLI]
  * [Node.js API]
  * [API in modules]
  * [Loaders]
  * [Plugins]
  * [Context]
  * [Resolving]
  * [Hot Module Replacement]
  * Dev Tools
    * [webpack-dev-server]
    * [webpack-dev-middleware]
* Development
  * [Changelog]
  * [Roadmap]
  * [Ideas]
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



[Motivation]:[docs/motivation.md]
[What is webpack? | What is webpack]:[docs/what-is-webpack.md]
[Installation]:[docs/installation.md]
[Usage]:[docs/usage.md]
[Using Loaders]:[docs/using-loaders.md]
[Using Plugins]:[docs/using-plugins.md]
[Dev Tools]:[docs/dev-tools.md]
[Troubleshooting]:[docs/troubleshooting.md]
[List of tutorials]:[docs/list-of-tutorials.md]
[examples]:[docs/examples.md]
[CommonJs]:[docs/commonjs.md]
[AMD]:[docs/amd.md]
[webpack for browserify users]:[docs/webpack-for-browserify-users.md]
[Code Splitting]:[docs/code-splitting.md]
[Stylesheets]:[docs/stylesheets.md]
[Optimization]:[docs/optimization.md]
[Long-term Caching]:[docs/long-term-caching.md]
[How to write a loader]:[docs/how-to-write-a-loader.md]
[How to write a plugin]:[docs/how-to-write-a-plugin.md]
[Multiple entry points]:[docs/multiple-entry-points.md]
[Library and externals]:[docs/library-and-externals.md]
[Shimming modules]:[docs/shimming-modules.md]
[Testing]:[docs/testing.md]
[Build performance]:[docs/build-performance.md]
[Hot Module Replacement with webpack]:[docs/hot-module-replacement-with-webpack.md]
[Comparison]:[docs/comparison.md]
[grunt | Usage with grunt]:[docs/usage-with-grunt.md]
[gulp | Usage with gulp]:[docs/usage-with-gulp.md]
[bower | Usage with bower]:[docs/usage-with-bower.md]
[karma | Usage with karma]:[docs/usage-with-karma.md]
[loader conventions]:[docs/loader-conventions.md]
[List of loaders]:[docs/list-of-loaders.md]
[List of plugins]:[docs/list-of-plugins.md]
[List of tutorials]:[docs/list-of-tutorials.md]
[List of hints]:[docs/list-of-hints.md]
[Configuration]:[docs/configuration.md]
[CLI]:[docs/cli.md]
[Node.js API]:[docs/node.js-api.md]
[API in modules]:[docs/api-in-modules.md]
[Loaders]:[docs/loaders.md]
[Plugins]:[docs/plugins.md]
[Context]:[docs/context.md]
[Resolving]:[docs/resolving.md]
[Hot Module Replacement]:[docs/hot-module-replacement.md]
[webpack-dev-server]:[docs/webpack-dev-server.md]
[webpack-dev-middleware]:[docs/webpack-dev-middleware.md]
[Changelog]:[docs/changelog.md]
[Roadmap]:[docs/roadmap.md]
[Ideas]:[docs/ideas.md]

