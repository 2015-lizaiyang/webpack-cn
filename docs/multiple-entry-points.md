前提条件: [代码拆分][Code Splitting]
如果你的项目要求多个为多哥HTML页面准备多个bundle时候你要用到这个功能。
它将一次性构建多个bundle。额外的chunk可以被这些入口chunk共用，并且他们的module只build一次。
> 注：使用 [代码拆分][Code Splitting]而不是将每个模块定义成入口文件。

每个入口文件，都包含着webpack运行环境。因而你只能给一个页面加载一个入口文件（想绕开这个限制，可以使用`CommonsChunkPlugin`将运行环境迁移到独立的chunk里面）。

## 配置

为了使用多入口文件，你可以给`entry`传入一个对象。对象的key代表入口点名字，value代表入口点。

当使用多入口点的时候，需要重载`output.filename`，否责每个入口点都卸乳到同一个输出文件里面了。使用`[name]`来得到入口点名字。

### 例子

``` javascript
{
	entry: {
		a: "./a",
		b: "./b",
		c: ["./c", "./d"]
	},
	output: {
		path: path.join(__dirname, "dist"),
		filename: "[name].entry.js"
	}
}
```

## 更多

* [multiple-entry-points](https://github.com/webpack/webpack/tree/master/examples/multiple-entry-points)
* [multi-part-library](https://github.com/webpack/webpack/tree/master/examples/multi-part-library)
* [multiple-commons-chunks](https://github.com/webpack/webpack/tree/master/examples/multiple-commons-chunks)

[Code Splitting]: code-splitting.md