为了有效的缓存你的文件，需要给文件URL添加hash或者版本号。你可以手动的把文件都放进一个带版本如`v1.3`的文件夹里面。但是这样做会有很多的缺陷：增加额外的工作，没有更改的文件也不能从缓存里面取之。

使用webpack可以给filename上加上hash，有些输出file的加载器已经支持（work-loader,file-loader）.对于chunks你还需要让他能够支持，两种级别：
1. 从所有的chunks中计算出一个hash值
2. 从每一个chunk中计算一个hash值


## 第一种：一个给bundle的hash

给文件名添加 `[hash]` 

配置选项:

`webpack ./entry output.[hash].bundle.js`

``` javascript
{
	output: {
		path: path.join(__dirname, "assets", "[hash]"),
		publicPath: "assets/[hash]/",
		filename: "output.[hash].bundle.js",
		chunkFilename: "[id].[hash].bundle.js"
	}
}
```

## 第二种: 每个chunk一个hash

给chunk文件名添加 `[chunkhash]`

config option

`--output-chunk-file [chunkhash].js`

```javascript
output: { chunkFilename: "[chunkhash].bundle.js" }
```

注意：如果你想在HTML中使用entry chunk的hash，你可能需要从stats中分离出相应文件的hash 或者filename。

如果是和热替换结合，你需要使用第一种类型但不能带`publicPath`配置选项

## 从stats中得到 filenames 

想要把从asset中得到的最终的filenames嵌入到HTML中，这些信息在webpack的stats 中是可以看到的。如果你使用CLI，运行脚本带上`--json`来得到stats的json文件

你也可以添加一个[assets-webpack-plugin](https://www.npmjs.com/package/assets-webpack-plugin) 的插件到wepack配置当中来让你得到stats对象。

或者自己写插件来得到它，例子：

``` javascript
plugins: [
  function() {
    this.plugin("done", function(stats) {
      require("fs").writeFileSync(
        path.join(__dirname, "...", "stats.json"),
        JSON.stringify(stats.toJson()));
    });
  }
]
```
stats JSON包含了一个有用的属性`assetsByChunkName`
The stats JSON contains a useful property `assetsByChunkName`
包含了一个以chunk name作为key，filename作为值的对象

> tips: 如果每个chunk都输出了多个asset那么filename会是一个数组. 比如一个chunk 你可能既输出js也输出sourceMap文件。
