To effectively cache your files, they should have a hash or version in their URL. You can emit or move the output files manually in a folder called `v1.3`. But this has several disadvantages: Extra work for the developer and unchanged files aren't loaded from cache.

Webpack can add hashes for the files to the filename. Loaders that emit files (worker-loader, file-loader) already do this. For the chunks you have to enable it. There are two levels:

1. Compute a hash of all chunks and add it.
2. Compute a hash per chunk and add it.

## Option 1: One hash for the bundle

Option 1 is enabled by adding `[hash]` to the filename config options:

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

## Option 2: One hash per chunk

Option 2 is enabled by adding `[chunkhash]` to the chunk filename config option

`--output-chunk-file [chunkhash].js`

```javascript
output: { chunkFilename: "[chunkhash].bundle.js" }
```

Note that you need to reference the entry chunk with its hash in your HTML. You may want to extract the hash or the filename from the stats.

In combination with Hot Code Replacement you must use option 1, but not on the `publicPath` config option.

## Get filenames from stats

You probably want to access the final filename of the asset to embed it into your HTML. This information is available in the webpack stats. If you are using the CLI you can run it with `--json` to get the stats as JSON to stdout.

You can add a plugin such as [assets-webpack-plugin](https://www.npmjs.com/package/assets-webpack-plugin) to your configuration which allows you to access the stats object. Here is an example how to write it into a file:

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

The stats JSON contains a useful property `assetsByChunkName` which is a object containing chunk name as key and filename(s) as value.

> Note: It's an array if you are emitting multiple assets per chunk. I. e. a JavaScript file and a SourceMap. The first one is your JavaScript source.
