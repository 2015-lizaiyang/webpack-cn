The CommonJS group defined a module format to solve 
JavaScript scope issues by making sure each module
is executed in its own namespace.

This is achieved by forcing modules to explicitly export
those variables it wants to expose to the "universe", 
and also by defining those other modules required to 
properly work.

To achieve this CommonJS give you two tools:

1. the `require()` function, which allows to import a given module into the current scope.
2. the `module` object, which allows to export something from the current scope.

The mandatory hello world example:

## Plain Simple JavaScript

Here is an example without CommonJS:

We will define a value in a script file named `salute.js`.
This script will contain just a value that will be used in other scripts:  

``` javascript
// salute.js
var MySalute = "Hello";
```

Now, in a second file named `world.js`, we are
going to use the value defined in `salute.js`.  

``` javascript	
// world.js
var Result = MySalute + " world!";
```

## Module definitions

As it is, `world.js` will not work as `MySalute` is not defined.
We need to define each script as a module:  

``` javascript
// salute.js
var MySalute = "Hello";
module.exports = MySalute;
```

``` javascript
// world.js
var Result = MySalute + "world!";
module.exports = Result;
```

Here we make use of the special object `module` and place a reference of our
variable into `module.exports` so the CommonJS module system knows this is 
the object of our module we want to show to the world.
`salute.js` discloses `MySalute`, and `world.js` discloses `Result`.

## Module dependency

We're near but there's still a step missing: dependency definition.
We've already defined every script as an independent module, but `world.js`
still needs to know who defines `MySalute`:

``` javascript
// salute.js
var MySalute = "Hello";
module.exports = MySalute;
```

``` javascript
// world.js
var MySalute = require("./salute");
var Result = MySalute + "world!";
module.exports = Result;
```

Note that we didn't use the full filename `salute.js` but `./salute` when calling 
`require`, so you can omit the extension of your scripts. `./` means that the `salute` module is in the same directory as the `world` module.


## Examples

### Functions

``` javascript
// moduleA.js
module.exports = function( value ){
	return value*2;
}
```

``` javascript
// moduleB.js
var multiplyBy2 = require('./moduleA');
var result = multiplyBy2( 4 );
```

