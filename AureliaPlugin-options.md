Inside `aurelia-webpack-plugin` are many plugins that implement the different pieces required to make webpack "work", which as we said in the introduction is adding missing dependencies and preserving module names at runtime.

What `AureliaPlugin` does is add and configure all those plugins into your webpack configuration, so that it is easy to use. If you have very complex build requirements, you could manage those plugins yourself instead of (or in addition to) `AureliaPlugin`.

`AureliaPlugin` has a few options to tailor it to your project's needs and some reasonable defaults for most projects. When getting started, you want to review those defaults to ensure that they work for your project.

Options are simply passed as an object literal to the constructor: 
```js
new AureliaPlugin({ "noHtmlLoader": true })
```

### `includeAll: false | string = false`
As will be explained in [[Managing dependencies]], precisely tracing dependencies in your source code requires a bit of instrumentation (namely `PLATFORM.moduleName()` calls).

`includeAll: "src"` is a shortcut that says: just grab everything in folder `src` and include it in the bundle, bypassing the instrumentation requirement. It's a quick, easy way to migrate existing applications. Note that when referring to external dependencies, you still need to wrap them into `PLATFORM.moduleName`, for example in a `.plugin('aurelia-datatable')` as they otherwise won't be in the bundle.

### `aureliaApp: string | undefined = "main"`
If you start your application with `aurelia-bootstrapper`, your startup module is probably in a `aurelia-app` attribute in your main HTML file. Because this file is generally not parsed by webpack, this initial dependency is lost.

That's why `AureliaPlugin` adds `aureliaApp` as a dependency to your entry point. By default it adds `main` but you can change it to another name like `app` or `index`.

If you have different startup code that doesn't require this initial dependency, you can remove it with `aureliaApp: undefined`.

### `aureliaConfig: string | string[] | undefined = ["standard", "developpmentLogging"]`
When starting an Aurelia app, you commonly call `aurelia.use.standardConfig()` and co. to enable the core default services.

This option makes sure all those services are dependencies of `aurelia-framework`. By default it includes everything, so that it just works.

If you don't use everything you can trim down your build by specifying what you actually use. All framework configurations have a matching string:
```js
"defaultBindingLanguage", "router", "history", "defaultResources", "eventAggregator", "developmentLogging",
"standard", "basic"
```

### `dist: string | undefined = "native-modules"`
This lets you easily switch the Aurelia distribution that you use. It adds a webpack resolver that tries to substitute `dist/xxx/` with `dist/[dist option]/` when resolving modules.

By default it is set to `native-modules`, which is a better choice than `commonjs` because it uses ES `import` and `export`, which support webpack tree-shaking.

### `features: { svg: boolean = true }`
This lets you remove Aurelia features that you don't use from a minified build. It works simply by defining global free variables with `DefinePlugin`.
- `svg: false -> FEATURE_NO_SVG = true` saves 20K but bindings on svg elements won't work anymore.

### `noHtmlLoader: boolean = false`
By default `AureliaPlugin` adds `html-resources-loader` to `.htm` and `.html` resources.
This loader detects Aurelia dependencies in views, like `<require from="...">`.

If the loader interferes with your build you can disable it by setting this option to `true`.

Note that if you don't use HTML views but another markup, you need to manually add `html-resources-loader` to your build.

### `noModulePathResolve: boolean = false`
This modules enables you to reference to files inside a package that might no be at the root.

For example, a library `aurelia-chart` might actually resolve to `aurelia-chart/dist/index.js`.
In that setup you normally can't refer to sibling files with `aurelia-chart/pie`, which is what you would use with `aurelia-loader`.

Thanks to that plugin the example `aurelia-chart/pie` would work and resolve to `aurelia-chart/dist/pie.js`.

If the plugin interferes with your build you can disable it by setting this option to `true`.

### `pal: string | undefined`
`AureliaPlugin` automatically bundles the correct `aurelia-pal-***` platform abstraction layer based on your webpack config `target`.

If you want a specific PAL module or none at all, you can use this option.

### `viewsFor: string = "src/**/*.{ts,js}"`
### `viewsExtensions: string | string[] | Function | Function[] = ".html"`
Aurelia uses conventions to locate views for custom elements or view models.
If your view model does not have `@noView` or `@useView("...")` or `@inlineView("...")` then Aurelia will just try to load a file with the same name but the extension swapped to `.html`.

This pair of options tries to add such dependencies automatically. 
For all files that match the glob `viewsFor`, it tries to resolve a module with the extension swap to `viewsExtensions` and adds it if it exists.

You typically would change `viewsFor` if your code is not in a `src` folder.

If you pass a `string` to `viewsExtensions` it will try a simple extension swap.
For more complex scenarios you can also pass a function `(filename: string) => string` that takes the current module name and returns the matching view module name.

[[Next: Managing dependencies|Managing dependencies]]