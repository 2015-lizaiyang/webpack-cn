"Hot Module Replacement" (HMR) is a feature to inject updated modules into the active runtime.

> It's like LiveReload for every module.

HMR is "opt-in", so you need to put some code at chosen points of your application. The dependencies are handled by the module system.

I. e. you place your hot replacement code in module A. Module A requires module B and B requires C. If module C is updated, and module B cannot handle the update, modules B and C become outdated. Module A can handle the update and new modules B and C are injected.

## Examples

#### Example 1: hot replace request handler of http server

``` javascript
var requestHandler = require("./handler.js");
var server = require("http").createServer();
server.on("request", requestHandler);
server.listen(8080);

// check if HMR is enabled
if(module.hot) {
	// accept update of dependency
	module.hot.accept("./handler.js", function() {
		// replace request handler of server
		server.removeListener("request", requestHandler);
		requestHandler = require("./handler.js");
		server.on("request", requestHandler);
	});
}
```

#### Example 2: hot replace css

``` javascript
// addStyleTag(css: string) => HTMLStyleElement
var addStyleTag = require("./addStyleTag");

var element = addStyleTag(".rule { attr: name }");
module.exports = null;

// check if HMR is enabled
if(module.hot) {

	// accept itself
	module.hot.accept();

	// removeStyleTag(element: HTMLStyleElement) => void
	var removeStyleTag = require("./removeStyleTag");

	// dispose handler
	module.hot.dispose(function() {
		// revoke the side effect
		removeStyleTag(element);
	});
}
```



## API

If HMR is enabled for a module `module.hot` is an object containing these properties:

### `accept`

``` javascript
accept(dependencies: string[], callback: (updatedDependencies) => void) => void
accept(dependency: string, callback: () => void) => void
```

Accept code updates for the specified dependencies. The callback is called when dependencies were replaced.

``` javascript
accept([errHandler]) => void
```

Accept code updates for this module without notification of parents. This should only be used if the module doesn't export anything. The `errHandler` can be used to handle errors that occur while loading the updated module.

### `decline`

``` javascript
decline(dependencies: string[]) => void
decline(dependency: string) => void
```

Do not accept updates for the specified dependencies. If any dependencies is updated, the code update fails with code `"decline"`.

``` javascript
decline() => void
```

Flag the current module as not update-able. If updated the update code would fail with code `"decline"`.

### `dispose/addDisposeHandler`

``` javascript
dispose(callback: (data: object) => void) => void
addDisposeHandler(callback: (data: object) => void) => void
```

Add a one time handler, which is executed when the current module code is replaced. Here you should destroy/remove any persistent resource you have claimed/created. If you want to transfer state to the new module, add it to `data` object. The `data` will be available at `module.hot.data` on the new module.

### `removeDisposeHandler`

``` javascript
removeDisposeHandler(callback: (data: object) => void) => void
```

Remove a handler.

This can useful to add a temporary dispose handler. You could i. e. replace code while in the middle of a multi-step async function.




## Management API

Also on the `module.hot` object.

### `check`

``` javascript
check([autoApply], callback: (err: Error, outdatedModules: Module[]) => void
```

Throws an exceptions if `status()` is not `idle`.

Check all currently loaded modules for updates and apply updates if found.

If no update was found, the callback is called with `null`.

If `autoApply` is truthy the callback will be called with all modules that were disposed. `apply()` is automatically called with `autoApply` as `options` parameter.

If `autoApply` is not set the callback will be called with all modules that will be disposed on `apply()`.

### `apply`

``` javascript
apply([options], callback: (err: Error, outdatedModules: Module[]) => void
```

If `status() != "ready"` it throws an error.

Continue the update process.

`options` can be an object containing these options:

* `ignoreUnaccepted`: If true the update process continues even if some modules are not accepted (and would bubble to the entry point).

### `status`

``` javascript
status() => string
```

Return one of `idle`, `check`, `watch`, `watch-delay`, `prepare`, `ready`, `dispose`, `apply`, `abort` or `fail`.

`idle`

The HMR is waiting for your call the `check()`. When you call it the status will change to `check`.

`check`

The HMR is checking for updates. If it doesn't find updates it will change back to `idle`.

If updates were found it will go through the steps `prepare`, `dispose` and `apply`. Than back to `idle`.

`watch`

The HMR is in watch mode and will automatically be notified about changes. After the first change it will change to `watch-delay` and wait for a specified time to start the update process. Any change will reset the timeout, to accumulate more changes. When the update process is started it will go through the steps `prepare`, `dispose` and `apply`. Than back to `watch` or `watch-delay` if changes were detected while updating.

`prepare`

The HMR is prepare stuff for the update. This may means that it's downloading something.

`ready`

An update is available and prepared. Call `apply()` to continue.

`dispose`

The HMR is calling the dispose handlers of modules that will be replaced.

`apply`

The HMR is calling the accept handlers of the parents of replaced modules, than it requires the self accepted modules.

`abort`

A update cannot apply, but the system is still in a (old) consistent state.

`fail`

A update has thrown an exception in the middle of the process, and the system is (maybe) in a inconsistent state. The system should be restarted.

### `status/addStatusHandler`

``` javascript
status(callback: (status: string) => void) => void
addStatusHandler(callback: (status: string) => void) => void
```

Register a callback on status change.

### `removeStatusHandler`

``` javascript
removeStatusHandler(callback: (status: string) => void) => void
```

Remove a registered status change handler.




## How to deal with ...

#### ... a module without side effects (the standard case)

Nothing to do in the module. Any parent can accept it.

#### ... a module with side effects

The module needs a dispose handler, then any parent can accept it.

#### ... a module with only side effects and no exports

The module needs a dispose handler and can accept itself. No action is required in the parent.

If the module's code is not in your hand, the parent can accept the module with some custom dispose logic.

#### ... the application entry module

As it doesn't export it can accept itself. A dispose handler can pass the application state on replacement.

#### ... external module with not handleable side effects

In the nearest parent you decline the dependency. This makes your application throw on update. But as it's an external module, an update is very rare.