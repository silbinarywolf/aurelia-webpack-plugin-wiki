Usually, you just use `AureliaPlugin` and be done. 
But in fact this plugin does nothing by itself.
It's a helper that sets up a lot of configuration and other plugins.
All those "other" plugins that actually do stuff are also exported by `aurelia-webpack-loader` and you can use them directly to further customize your build.

Here's a list of the ones you are likely to use.

## ModuleDependenciesPlugin
`new ModuleDependenciesPlugin({ [module: string]: (string | DependencyEx)[] })`

This plugin lets you add dynamic dependencies between modules that are not found in code.
Each key is the parent module and each value is the list of modules that it depends on.
You can find more information, including examples on page [Managing dependencies](Managing dependencies).

## ConventionDependenciesPlugin
`new ConventionDependenciesPlugin(glob: string, conventions: string | Convention | (string|Convention)[])`

This plugin lets you add dynamic dependencies automatically based on file names.
Typical usage is to find and add `.html` views for `.js|.ts` view models in your project.

- `glob` is a Glob that filters which files this plugin is applied to. 
- `conventions` are the conventions that the plugin tries to apply. Simple strings like `.html` are extensions swap. Otherwise you can pass functions that return arbitrary dependencies `(filename: string) => string`, for example if your views are in a different folder.

The plugin probes the filesystem for each convention and if it fails, nothing happens.

By default, `AureliaPlugin` adds the convention `("**/*.{ts,js}", ".html")`, which is the default Aurelia view convention.
You can reconfigure that default (for example to use `.pug` views) with [options `viewsFor` and `viewsExtensions`](https://github.com/jods4/aurelia-webpack-build/wiki/AureliaPlugin-options#viewfor-and-viewextensions).

You can of course add additional `ConventionDependenciesPlugin` instances if you need more than one convention.

## GlobDependenciesPlugin
`new GlobDependenciesPlugin({ [module: string]: string | string[] })`

This module is similar to `ModuleDependenciesPlugin` above, but instead of adding specific dependencies to a module, it adds every file that match one (or several) Globs.
**Note that globs are evaluated relative to your webpack config location.**

This is what `AureliaPlugin` uses to implement its [`includeAll` option](https://github.com/jods4/aurelia-webpack-build/wiki/AureliaPlugin-options#includeall).

