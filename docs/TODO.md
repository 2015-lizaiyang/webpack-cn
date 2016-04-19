## wiki

[[hot-module-replacement-with-webpack]]

[[internal-webpack-plugins]]

imports-loader and window.jQuery

support for path-to-regexp

moment.js

so i have a couple of things i think should probably be in the docs: 1) best practice for shimming browser js (i.e. a jquery plugin), 2) how to configure for altjs languages, 3) how to configure webpack for browser package managers like bower.
1) => http://webpack.github.io/docs/shimming-modules.html
2) => How to write a loader; http://webpack.github.io/docs/using-loaders.html
3) => Vendor Modules
Some of them are still work in progress
dontkry may be a good start for 3)

## Create a enhanced require function

``` javascript
var myRequire = require("enhanced-require")(module, {
	// options
});

// startup your application
myRequire("./startup");
```

## Usage

Than you can use them:

``` javascript
// use loaders
var fileContent = require("raw!"+__filename);

// use loaders automatically
var template = require("./my-template.jade");
// you need to pass this options: 
// { module: { loaders: [ { test: /\.jade$/, loader: "jade" } ] } }

var html = template({content: fileContent});

// use require.context
var directoryRequire = require.context("raw!./subdir");
var txtFile = directoryRequire("./aFile.txt");

// use require.ensure
require.ensure(["./someFile.js"], function(require) {
	var someFile = require("./someFile.js");
});

// use AMD define
require.define(["./aDep"], function(aDep) {
	aDep.run();
});

// use AMD require
require(["./bDep"], function(bDep) {
	bDep.doSomething();
});
```

## Hot Code Replacement

``` javascript
require("enhanced-require")(module, {
	hot: true, // enable hot code replacement
	watch: true // watch for changes
})("./startup");
```

For hot code reloading you need to follow the [hot code reloading spec](https://github.com/webpack/enhanced-require/wiki/HCR-Spec).

## Testing/Mocking

``` javascript
var er = require("enhanced-require");
it("should read the config option", function(done) {
	var subject = er(module, {
		substitutions: {
			// specify the exports of a module directly
			"../lib/config.json": {
				"test-option": { value: 1234 }
			}
		},
		substitutionFactories: {
			// specify lazy generated exports of a module
			"../lib/otherConfig.json": function(require) {
				// export the same object as "config.json"
				return require("../lib/config.json");
			}
		}
	})("../lib/subject");

	var result = subject.getConfigOption("test-option");
	should.exist(result);
	result.should.be.eql({ value: 1234 });
});
```



---

# from commonjs

## Differences between CommonJS and RequireJS

There are two main differences between CommonJS and RequireJS.

The first one is how modules are defined. 
While CommonJS uses its own method (seen above), RequireJS implements
the AMD (Asynchronous Module Definitions) specification.

The second difference is how dependencies are loaded.
While CommonJS expects `require` calls to behave synchronously, 
RequireJS loads its modules asynchronously, behaving more
accordingly as how the browser works.
This heavily marks where to use each of these two module systems, 
CommonJS is used mainly in server JavaScript implementations (Nodejs),
while RequireJS is headed to be used in the browser.
