A list of open ideas.

* Support globs in require statements so you can easily bundle static assets etc. Webpack-dev-server should monitor the glob for new files.
* Allow defining plugins in the webpack configuration file so you don't need an extra file for trivial custom loaders
* Allow adding command line parameters to the webpack CLI from the configuration file so it's easier to change the webpack configuration inside the configuration file.
* Allow the option of excluding of some paths (eg 'node_modules') from triggering warnings / errors.  Example use case: ProtobufJS triggers a bunch of warnings - it's not relevant to me.
* Allow watching of files in webpack-dev-server specifically (using glob). If using something like PostCSS or Sass, changes in `@import`-ed CSS files aren't watched.