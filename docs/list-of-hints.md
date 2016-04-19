> **Notes**:

> Not every hint apply to all apps. Some hints have positive and negative effects so it **depends on your needs**.

> Hints are **ordered by importance** (most important comes first), but importance heavily depends on the app.

> Hints are categorized by App, Developer and/or Build performance. Sometimes multiple categories apply.

> **App performance**: Your app perform better. This affects the user of your app and/or the cost of serving the app to the user.

> **Developer performance**: This makes it easier for your developers to write the app.

> **Build performance**: The build of your app is faster and/or more stable.

## App performance

* Minimize your bundle with the `UglifyJsPlugin` (App, for every app)
* Use Code Splitting: improves initial download size, at the cost of more requests (App, for big apps)
  * Hint for React apps: Use the react-proxy-loader
* Add hashes to output files and enable Long Caching time on server: improves times for second visit (App, for every app)
  * Hint: Use records to keep module/chunk ids as consistent as possible
  * Hint for static HTML pages: Use the html-webpack-plugin
* Don't delete no longer used assets immediately after they are no longer used. Wait a few weeks before deleting them from server. Result: No 404s for users that keep browser windows open for long time (App, for every app)
* Use the `DefinePlugin` to pass configuration from config to app: Embedded into bundle, Conditional code is removed with minimized (App, for apps with configuration)
  * Use the `EnvironmentPlugin` to pass `process.env` from build to app
* Check bundle stats with analyse tool for problems: Improve total download size, Improve cohesion (App/Developer, for big apps)
  * Hint: Use the `stats-webpack-plugin` or the `--json` CLI option to get the stats
  * Hint: Use the `profile` option to gather more performance stats
* Extract common modules into separate script file: improves caching for switching between pages, at the cost of additional requests for the initial page (App, for app with multiple entry points)
* Remove duplication with `npm dedupe`/`npm 4` and the `DedupePlugin`: improve total download size (App, for app using npm)
* Do CSS processing with webpack:  (App/Developer, for every app)
  * static assets (font/image/...) processing with webpack. (for every app)
    * inline static assets with the url-loader: improves time to initial view (by reducing roundtrips), at the cost of total download size (for every app)
  * Separate CSS file with the extract-text-webpack-plugin: eliminates FOUC for prerendered markup, improves time to initial view (by parallizing CSS and JS downloading) (for app with many CSS or prerendered content)
* Fit the chunking to your needs via many Code Splitting Points and the chunk optimization plugins (`LimitChunkCountPlugin`, `MinChunkSizePlugin`, `AggressiveMergingPlugin`, )
* Preload additional chunks by adding a script tag and deferring the chunk load: Faster initial view (App, for routed apps)

## Developer performance

* Use a configuration file (webpack.config.js) instead of passing CLI options: Easier to maintain, more options (Developer, for every app)
* Don't rewrite incompatible JS, but use `imports-loader`/`exports-loader` to make it compatible: Easier to upgrade to new version (Developer, for every app)
* Use webpack devtools for debugging in browser: Better debugging experience, real source code, real module names, at the cost of slower build and difference to production build (Developer, for every app)
* Write modules with ES6 module syntax: This is more future proof and allows more advanced optimizations (Developer/App/Build, for every app)
  * Current Status: Use the `babel-loader` to transform ES6 module syntax to CommonJS
  * Future: webpack 2 understand ES6 module syntax
  * Future: webpack enables advanced optimizations
* Use `output.library` to build libraries that export stuff (Developer, for libraries)
* Use `externals` to declare dependencies of your bundle on the target environment (Developer, for libraries and apps)
* Enable Hot Module Replacement (HMR) for faster page updates (Developer, for every app)
  * Hint for React apps: Use the `react-hot-loader` or `react-transform`
  * Hint for CSS: Use the `style-loader` (without `extract-text-webpack-plugin`) for HMR
  * Hint for custom routers: Write custom handlers for updates at least at router level
* Use Javascript in webpack config to share common configuration etc. (Developer, for every app)
* Use `resolve.root` to configure a path to your app modules: Allows shorter references to dependencies (Developer, for big apps)
* Use `karma` with `karma-webpack` to test modules in the browser (Developer, for every app)
* Use `target` to build for other environments than the browser (Developer, for non-browser apps)
* Use the `BannerPlugin` to add comments to the output assets: Licensing (Developer, for libraries)
* Use `debug` to switch loaders to debug mode which provide more debug information (if the loader supports it): Better debugging experience (Developer, for every app)
* Use `include` instead of `exclude` in `module.loaders`: less error prone and easier to add paths (Developer, for every app)

## Build performance

* Use incremental compilation: faster second build (Build, for every app)
  * Hint: Switch watching to polling with the `watchOptions.poll` option only if watching over network or inside of VMs
* Use in-memory compilation for development build: faster build, less disk usage, at the cost of memory usage (Build, for every app)
  * Hint: Use the webpack-dev-server
* Use multiple entry points instead of running webpack multiple times: Faster build, entry points can share chunks (Build/App, for multi page apps)
* Pass an array of configurations to webpack to compile them in parallel while sharing disk cache and watchers: Faster builds and rebuilds, less problems with too many watchers (Build, for big apps with multiple configurations)
* Use `module.noParse` for big CommonJS files without dependencies: Faster build times (Build, for app with these modules)

## Unsorted

> Feel free to add more hints to any category. If you were to add it or in which order you can add it to the `Unsorted` section and some more experienced user will pick it up.