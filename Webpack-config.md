Here's what a typical `webpack.config.js` looks like for an Aurelia project: 
```js
const path = require("path");
const { AureliaPlugin } = require("aurelia-webpack-plugin");
const coreDeps = require("aurelia-core-dependencies");

module.exports = {
  entry: { "main": "aurelia-bootstrapper" },  // (1)

  output: {                                   // (2)
    path: path.resolve(__dirname, "dist"),
    publicPath: "dist",
    filename: "[name].js",    
    chunkFilename: "[name].js",
  },

  resolve: {                                  // (3)
    extensions: [".ts", ".js"],
    modules: ["src", "node_modules"],
  },

  module: {                                   // (4)
    rules: [
      { test: /\.less$/i, use: ["style-loader", "css-loader", "less-loader"] },
      { test: /\.ts$/i, use: "ts-loader" },
      { test: /\.html$/i, use: "html-loader" },
    ]
  },  

  plugins: [
    new AureliaPlugin(),                       // (5)
    coreDeps,                                  // (6)
  ],
};
```

Let's look at the numbered references in more details.

### (1) Entry point
This is your normal webpack entry point. It defines the module that will be executed when the bundle is loaded. Nothing special here.

For an Aurelia application, it is fairly typical to start with `aurelia-bootstrapper`, which will scan the DOM for `aurelia-app` attributes and use those to find the module that configures and starts the application. As will be explained later, `AureliaPlugin` by default assumes `aurelia-app="main"` but you can specify another module name with the `aureliaApp` option.

You can use an entry point different from `aurelia-bootstrapper`, for example if you start Aurelia manually. In this case you'll probably want to tell `AureliaPlugin` that it doesn't need to include a `main` module with `aureliaApp: undefined`.

In the example above the webpack entry point is explicitly named. This plays along well with multiple chunks, as set up in `output` (see below). It's all standard webpack and you can go with `entry: "aurelia-bootstrapper"` if the output is OK for you.

### (2) Output
Again this is all standard webpack. It defines the structure of the output files.

Note that the example above is properly set up to write to (and serve from) a `/dist` folder. It is also set up to support multiple chunks, if you want to do code splitting (i.e. loading some modules on demand rather than at startup).

If you want to write everything into a single bundle file, you can go with the simpler: 
```js
  entry: "aurelia-bootstrapper",

  output: {
    path: path.resolve(__dirname, "dist"),
    publicPath: "dist",
    filename: "bundle.js",    
  },
```

### (3) Resolve
Once again, normal webpack config. 

Here we add `.ts` to the default extensions list, so that we can use Typescript source code. Of course you don't need to do that if you don't use Typescript. And since this is "just" webpack, you can use other languages if you want, like Coffeescript or Dart.

The `module` list is slightly more important. It tells webpack where to look when resolving absolute references (not starting with a `.`). The example is typical in that it indicates the root of our application source tree, and `node_modules` for libraries. 

But `module` is also used by `AureliaPlugin` as the list of root folders to use when normalizing runtime module ids. For all dependencies loaded by `aurelia-loader`, the plugin will try to build an id that is rooted in one of those folders.

### (4) Module
The example is the standard webpack config to use LESS, Typescript and HTML. None of those are requirements, you can change this to anything you want.

If you don't use HTML source files for your views, there is one catch that you need to be aware of. As will be explained later, `AureliaPlugin` adds a loader for all HTML files to analyze them and detect their dependencies (for example local resources imported with `<require from="...">` tags). If you don't use HTML sources, you'll need to add `html-resource-loader` manually for your views.

### (5) Aurelia Plugin
This is where the magic happens. `AureliaPlugin` augments your webpack configuration and adds several other plugins (and a loader) so that all dynamic `aurelia-loader` requests are properly detected and added as Webpack dependencies.

The plugin assumes a few default options that may not be right for your project. We'll review those next so that you can configure it properly. Its constructor accepts an option object like that:
```js
new AureliaPlugin({ aureliaApp: "app", dist: "commonjs" })
```

### (6) Aurelia-Core-Dependencies
This is a temporary plugin that defines internal Aurelia dependencies. It won't be necessary once the published Aurelia release defines these dependencies itself. Just use it for now.

[[Next: let's review AureliaPlugin's options|AureliaPlugin options]]