Finally, here are a few tips if some module is missing, which invariably happens sooner or later.

### Webpack can't resolve a module
This one is usually not related to `AureliaPlugin`.

If you get a red error from webpack at build-time that says it can't resolve a module, look at the module name, look at where you'd expect it to be found, look at your webpack config. "Classical" webpack debugging hints apply.

### "Can't find module Id" at runtime
If the application is not working at runtime, try to break on all exceptions.
It usually ends up on `aurelia-loader` throwing an exception because it can't find a module.
Remember which ID it can't load and start investigating.

If you run `webpack --display-modules` it will display all included modules in its output, with (first) their runtime module id and (second) the file that it resolved to.

The first question you should ask yourself: "is the file in the bundle with an incorrect id or is it missing completely?".

Most of the time it is completely missing. 
Ask yourself why/where it should have been included. What code has a dependency on it? 
- If it's in your code you probably lack a `PLATFORM.moduleName()` somewhere. 
- If it's in a library, then it probably doesn't use `moduleName`. You can easily fix this with `ModuleDependenciesPlugin` (see [[Managing dependencies]]).

If the file is there but doesn't have the correct id, it gets more complicated.
- Be sure to require the file with `PLATFORM.moduleName()` or some `aurelia-webpack-plugin` facilities. If it was included because it is a static `import` dependency of another module, its name won't be preserved and you'll end up with an integer id.
- If you're sure it was included by `AureliaPlugin` but the id is still an integer, this is usually because the plugin could not normalize the module name. In particular, only files inside your webpack `resolve.module` folders can be normalized. Don't build relative paths outside of those (e.g. `/../../something` from `/src`).
- If the module name is a string but not the correct/expected path, then something weird is going on.

> **TODO**: explain how we support webpack `alias` in advanced scenarios.