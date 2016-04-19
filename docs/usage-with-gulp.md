
结合gulp使用和[node.js API](node.js-api.html)一样简单.

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

上面的例子，将 ```src/entry.js``` 用webpack打包到```dist/``` 当中，输出的文件名，由webpack的build hash ```[hash].js``` 产生

你也可以将`webpack.config.js`传到gulp流里面：
```zsh
    return gulp.src('src/entry.js')
      .pipe(webpack( require('./webpack.config.js') ))
      .pipe(gulp.dest('dist/'));
```

可以查看更多 [webpack-stream](https://github.com/shama/webpack-stream) 相关。

## 不使用 `webpack-stream`
```zsh
    var gulp = require("gulp");
    var gutil = require("gulp-util");
    var webpack = require("webpack");
    var WebpackDevServer = require("webpack-dev-server");
```

### 一般的编译过程

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

## webpack-dev-server

> `webpack-dev-server`是一个重要的辅助开发的工具

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
## 举例子

例子包含下面三个部分

*   webpack-dev-server
*   build - watch cycle
*   production build

[gulpfile demo](https://github.com/webpack/webpack-with-common-libs/blob/master/gulpfile.js)

