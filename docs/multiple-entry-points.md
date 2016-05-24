前提条件: [代码拆分][Code Splitting]
如果你的项目要求多个为多哥HTML页面准备多个bundle时候你要用到这个功能。
它将一次性构建多个bundle。额外的chunk可以被这些入口chunk共用，并且他们的module只build一次。
If you need multiple bundles for multiple HTML pages you can use the "multiple entry points" feature. It will build multiple bundles at once. Additional chunks can be shared between these entry chunks and modules are only built once.

> Hint: When you want to start an entry chunk from a module, you are doing something wrong. Use [[Code Splitting]] instead!

Every entry chunk contains the webpack runtime, so you can only load one entry chunk per page. (Hint: To bypass this limitation use the CommonsChunkPlugin to move the runtime into a single chunk.)

## Configuration

To use multiple entry points you can pass an object to the `entry` option. Each value is threaded as entry point and the key represents the name of the entry point.

When using multiple entry point you must override the default `output.filename` option. Otherwise each entry point would write to the same output file. Use `[name]` to get the name of the entry point.

### Minimal example configuration

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

## Examples

* [multiple-entry-points](https://github.com/webpack/webpack/tree/master/examples/multiple-entry-points)
* [multi-part-library](https://github.com/webpack/webpack/tree/master/examples/multi-part-library)
* [multiple-commons-chunks](https://github.com/webpack/webpack/tree/master/examples/multiple-commons-chunks)

[Code Splitting]: code-splitting.md