
Using webpack with gulp is as easy as using the

和[node.js API](node.js-api.html)一样简单.

## 运用 [webpack-stream](https://github.com/shama/webpack-stream)
```zsh
    var gulp = require('gulp');
    var webpack = require('webpack-stream');
    gulp.task('default', function() {
      return gulp.src('src/entry.js')
        .pipe(webpack())
        .pipe(gulp.dest('dist/'));
    });
```

The above will compile src/entry.js into assets with webpack into dist/ with the output filename of [hash].js (webpack generated hash of the build).

Or just pass in your `webpack.config.js`:
```zsh
    return gulp.src('src/entry.js')
      .pipe(webpack( require('./webpack.config.js') ))
      .pipe(gulp.dest('dist/'));
```

See [webpack-stream](https://github.com/shama/webpack-stream) for more options and details.

## [→](#without-webpack-stream)Without `webpack-stream`
```zsh
    var gulp = require("gulp");
    var gutil = require("gulp-util");
    var webpack = require("webpack");
    var WebpackDevServer = require("webpack-dev-server");
```

### [→](#normal-compilation)Normal compilation

```zsh
    gulp.task("webpack", function(callback) {
        // run webpack
        webpack({
            // configuration
        }, function(err, stats) {
            if(err) throw new gutil.PluginError("webpack", err);
            gutil.log("[webpack]", stats.toString({
                // output options
            }));
            callback();
        });
    });
    
```

## [webpack-dev-server](webpack-dev-server.html)

> Don’t be too lazy to integrate the webpack-dev-server into your development process. It’s an important tool for productivity.

```zsh
    gulp.task("webpack-dev-server", function(callback) {
        // Start a webpack-dev-server
        var compiler = webpack({
            // configuration
        });

        new WebpackDevServer(compiler, {
            // server and middleware options
        }).listen(8080, "localhost", function(err) {
            if(err) throw new gutil.PluginError("webpack-dev-server", err);
            // Server listening
            gutil.log("[webpack-dev-server]", "http://localhost:8080/webpack-dev-server/index.html");

            // keep the server alive or continue?
            // callback();
        });
    });
```
## [→](#example)Example

Take a look at an example gulpfile. It covers three modes:

*   webpack-dev-server
*   build - watch cycle
*   production build

[Example gulpfile](https://github.com/webpack/webpack-with-common-libs/blob/master/gulpfile.js)
