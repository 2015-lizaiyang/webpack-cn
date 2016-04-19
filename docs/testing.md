There are two ways to test web applications:

* In-browsers: You get a more realistic test, but you need some more complex infrastructure and the test usually take longer. You can test DOM access.
* with node.js: You cannot test DOM access, but testing is usually faster.

## In-browser testing

### mocha-loader

The mocha-loader executes your code with the mocha framework. If you run the code it'll show the results in the web page.

*Hint: when using `!` in the bash command line, you must escape it by prepending a `\`*

``` sh
webpack 'mocha!./test.js' testBundle.js
# index.html is a HTML page which loads testBundle.js
open index.html
```

### webpack-dev-server

The webpack-dev-server will automatically create a HTML page which loads the script. It also re-executes the tests when files have changed.

``` sh
webpack-dev-server 'mocha!./test.js' --hot --inline --output-filename test.js
open http://localhost:8080/test
```

Hint: Use `--hot` and it'll only execute tests which have changed or have changed dependencies.

### karma and webpack

You can use webpack with karma. Add [`"webpack"` as preprocessor](https://github.com/webpack/karma-webpack) to your karma config.


## node.js testing

### CommonJs only

If you write your web app only in CommonJs and don't use loaders or other webpack-specific features, you can test it in node.js. Just use a node.js testing framework, i. e. [mocha](http://visionmedia.github.io/mocha/).

``` sh
mocha test/*
```

### Compile and test

If you use webpack-specific features it may not be possible to run the code with node.js. webpack allows to configure a target system: i. e. you can compile code so that it can run in node.js (configuration option `target: "node"`). Then use a node.js testing framework to run the bundle.

``` sh
webpack test.js /tmp/testBundle.js --target node
mocha /tmp/testBundle.js
```

> Hint: You can use the `null-loader` for stylesheets instead of the `style-loader!css-loader`. `style-loader` doesn't work in node.js as it requires a DOM.

To make debugging tests easier, you can add source map support using [node-source-map-support](https://github.com/evanw/node-source-map-support):

```sh
webpack test.js /tmp/testBundle.js --target node
mocha --require source-map-support/register /tmp/testBundle.js
```

Make sure to configure the [`devtool`](http://webpack.github.io/docs/configuration.html#devtool) option to output the source map.

### enhanced-require

TODO