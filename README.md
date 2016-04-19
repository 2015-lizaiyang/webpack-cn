## webpack doc
中文翻译
![[ds](stephenzhao.github.io/webpack_cn_doc/)](QQ20160303-0.png)
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


# Summary

* [关于文档](README.md)
* [概览](chapter0/home.md)
* 开始
   * [动机](chapter1/dong_ji.md)
   * [Webpack是什么](chapter1/webpack_is.md)
   * [安装](chapter1/an_zhuang.md)
   * [使用方法](chapter1/usage.md)
   * Require Modules
   * Vendor Modules
   * [Using Loaders](chapter1/using_loaders.md)
   * [使用插件](chapter1/using_plugins.md)
   * [工具](chapter1/devtool.md)
   * [常见问题](chapter1/troubleshooting.md)
* 教程和例子
   * [开始](chapter2/gettingstarted.md)
   * [教程列表](chapter2/listof_tutorials.md)
   * 例子
* 实践指南
   * Commonjs
   * AMD
   * browserify用户
   * [代码拆分](chapter3/codeSplitting.md)
   * Stylesheets
   * [优化](chapter3/Optimization.md)
   * [长期缓存](chapter3/long_term_cashing.md)
   * [如何编写loader](chapter3/ru_he_bian_xie_loader.md)
   * [如何编写插件](chapter3/ru_he_bian_xie_cha_jian.md)
   * 多个入口的代码分割
   * 库和扩展
   * SHIMMING MODULES
   * 测试
   * 编译性能检测
   * 热替换
   * 各模块加载器对比
* 结合其他工具
   * [gulp](chapter4/gulp.md)
* 一些资源列表
   * loaders列表
* API
   * [Configuration](chapter6/configuration.md)

