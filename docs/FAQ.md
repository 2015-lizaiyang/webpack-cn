## How do I use jQuery?

For versions of jQuery greater than or equal to 1.10, you shouldn't need to do anything special: just make sure that webpack can find jQuery  and `require('jquery')` in your modules.

If you're using 1.9 or less, you'll need to explicitly tell jQuery that webpack wants it to expose itself as an AMD module by adding this to your config:

```javascript
{
    amd: {
        jQuery: true
    }
    ...
}
```

See the [[amd configuration option|configuration#amd]] for more information.


## How do I use CoffeeScript (or [other compile-to-js languages](https://github.com/jashkenas/coffee-script/wiki/List-of-languages-that-compile-to-JS))?

webpack can load altJS languages with special loaders. For example,

```javascript
require('coffee-loader!mymodule.coffee')
```

However, you probably don't want to specify a loader and extension every time you require your module. Avoiding that requires two steps: 1) configuring webpack to use a special loader for files of the given language and 2) configuring webpack to find modules written in that language. For CoffeeScript, that looks like this:

```javascript
{
    module: {
        loaders: [
            {test: /\.coffee$/, loader: 'coffee-loader'},  // Use the CoffeeScript loader for *.coffee files
            ...
        ],
        ...
    },
    resolve: {
        extensions: ['', '.webpack.js', '.web.js', '.js', '.coffee'],  // Look for *.coffee files when resolving modules
        ...
    },
    ...
}
```

A few things to note:

* The loaders are not bundled with webpack and must be installed separately.
* In order to retain the original resolution behavior, your extension array must include the default values (as shown above). See [[resolve.extensions|configuration#resolve-extensions]] for more details.



## How do I integrate Webpack with Rails

Justin Gordon has created a detailed document describing how to integrate Webpack with Rails, including ReactJS and ES6, and deployment on Heroku: [Fast Rich Client Rails Development With Webpack and the ES6 Transpiler](http://www.railsonmaui.com/blog/2014/10/02/integrating-webpack-and-the-es6-transpiler-into-an-existing-rails-project/).

