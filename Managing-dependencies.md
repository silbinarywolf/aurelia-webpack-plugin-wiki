Half of `AureliaPlugin` is about detecting dependencies.

### PLATFORM.moduleName()
This is normally done by indicating module names in code by wrapping them in `PLATFORM.moduleName()` from the PAL:
```js
import { PLATFORM } from "aurelia-pal";

aurelia.use.plugin(PLATFORM.moduleName("aurelia-validation"));
```
The build will remove all those calls and just leave the string in its place.

Here are a few common Aurelia APIs that take module names:
- Routes `{ moduleId: "xxx" }`
- `.plugin("xxx")`
- `.feature("xxx")` **But see below!**
- `.globalResources("xxx")`
- `@useView("xxx")`
- `.setRoot("xxx")`

All of those must be wrapped inside `PLATFORM.moduleName("xxx")`.

> `.feature("xxx")` is currently tricky, because it doesn't take a module name but its parent folder and works by appending `/index`. At the moment, to work around it you can put a standalone `moduleName` next to it like so:
```js
aurelia.feature("controls");
PLATFORM.moduleName("controls/index");
```

### HTML dependencies 
A few Aurelia tags can dynamically load modules, like:
- `<require from=...>`
- `<compose view=... viewModel=...>`

Those are detected by `html-resources-loader`.
If you need to you can add more attributes with the following code:
```js
const { attributes } = require("aurelia-webpack-plugin/dist/html-requires-loader");
attributes["my-tag"] = [ "some-attribute" ];
```

### ModuleDependenciesPlugin
The long term goal is that all Aurelia libraries adopt `PLATFORM.moduleName` and "just work".
Unfortunately right now none of them do, which means their dependencies won't be properly included.

If you encounter a problem with a missing dependency, `ModuleDependenciesPlugin` is an easy way to fix it in your build configuration. 
With this plugin, you can manually define additional dependencies for any module in your build. 
Here's one example:
```js
import { AureliaPlugin, ModuleDependenciesPlugin } from "aurelia-webpack-plugin";
module.exports = {
  // ...
  plugins: [
    new AureliaPlugin(),
    new ModuleDependenciesPlugin({
      "aurelia-charting": [ "./pie", "./bar", "./line" ],
      "parent-module": [ "child-module" ],
    }),
  ]
};
```

### Code splitting
You can put a dependency into a different webpack chunk so that it is loaded on demand rather than at startup.

To do so you just need to pass a chunk name as the second parameter to `moduleName`:
```js
let routes = [
  { /*...*/ moduleId: PLATFORM.moduleName(/*module:*/"users", /*chunk:*/"admin") }
];
```

You can do similarly when using `ModuleDependenciesPlugin`:
```js
new ModuleDependenciesPlugin({
  "parent-module": [ { name: "module-dependency", chunk: "chunk-name" } ]
});
```

For more information about chunks and code splitting, you can refer to standard webpack documentation.

[[Next: Debugging missing modules|Debugging missing modules]]