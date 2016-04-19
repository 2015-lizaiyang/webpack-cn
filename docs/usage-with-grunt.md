There is a grunt plugin for using webpack and the [[webpack-dev-server]]: [grunt-webpack](https://github.com/webpack/grunt-webpack).

It's pretty simple to use:

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

## Development

The best option for development is the [[webpack-dev-server]], but it requires spawning a server. If this is not possible or too complex the normal build - watch cycle is possible too.

## Example

Take a look at an example Gruntfile. It covers three modes:

* webpack-dev-server
* build - watch cycle
* production build

[Example Gruntfile](https://github.com/webpack/webpack-with-common-libs/blob/master/Gruntfile.js)