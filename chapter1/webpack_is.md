
**webpack** is a **module bundler**.

webpack takes modules with dependencies and generates static assets representing those modules.

![modules with dependencies ---webpack---> static assets](http://webpack.github.io/assets/what-is-webpack.png)

## [→](#why-another-module-bundler)Why another module bundler?

Existing module bundlers are not well suited for big projects (big single page applications). The most pressing reason for developing another module bundler was [Code Splitting](code-splitting.html) and that static assets should fit seamlessly together through modularization.

I tried to extend existing module bundlers, but it wasn’t possible to achieve all goals.

## [→](#goals)Goals

*   Split the dependency tree into chunks loaded on demand
*   Keep initial loading time low
*   Every static asset should be able to be a module
*   Ability to integrate 3rd-party libraries as modules
*   Ability to customize nearly every part of the module bundler
*   Suited for big projects

## [→](#how-is-webpack-different)How is webpack different?

#### [→](#code-splitting)[Code Splitting](code-splitting.html)

webpack has two types of dependencies in its dependency tree: sync and async. Async dependencies act as split points and form a new chunk. After the chunk tree is optimized, a file is emitted for each chunk.

Read more about [Code Splitting](code-splitting.html).

#### [→](#loaders)[Loaders](loaders.html)

webpack can only process JavaScript natively, but loaders are used to transform other resources into JavaScript. By doing so, every resource forms a module.

Read more about [Using loaders](using-loaders.html) and [Loaders](loaders.html).

#### [→](#clever-parsing)Clever parsing

webpack has a clever parser that can process nearly every 3rd party library. It even allows expressions in dependencies like so `require(<span class="string">"./templates/"</span> + name + <span class="string">".jade"</span>)`. It handles the most common module styles: [CommonJs](commonjs.html) and [AMD](amd.html).

Read more about [expressions in dependencies](context.html), [CommonJs](commonjs.html) and [AMD](amd.html).

#### [→](#plugin-system)[Plugin system](plugins.html)

webpack features a rich plugin system. Most internal features are based on this plugin system. This allows you to customize webpack for your needs and distribute common plugins as open source.

Read more about [Plugins](plugins.html).
