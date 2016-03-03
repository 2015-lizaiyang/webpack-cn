## webpack doc
中文翻译

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