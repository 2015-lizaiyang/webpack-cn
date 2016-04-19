
## Installation

Installation is via npm:

    npm install webpack -g

## Compiling JavaScript

Give Webpack an entry point and an output file:

    webpack ./app.js app.bundle.js

## Creating a JavaScript module

We can define modules in JavaScript using the CommonJS syntax, as for Node:

**cats.js**

    var cats = ['dave', 'henry', 'martha'];
    module.exports = cats;

**app.js**

    cats = require('./cats.js');
    console.log(cats);

## FURTHER READING

Recommended reading: [Webpack your bags](http://blog.madewithlove.be/post/webpack-your-bags/) by [Maxime Fabre](https://twitter.com/anahkiasen) - a very good introduction on how to setup a real-world project using Webpack.

* see [[CLI]] for the command line interface.
* see [[node.js API]] for the node.js interface.
* see [[Configuration]] for the configuration options.