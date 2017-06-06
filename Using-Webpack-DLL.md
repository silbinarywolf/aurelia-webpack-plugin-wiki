This is a brief how-to guide to use Webpack DLL in Aurelia projects. 
It focuses on the Aurelia part and does not replace a good introduction to Webpack DLL.

## What is a DLL anyway?
A Webpack DLL is a feature that allows you to re-use the result of one Webpack compilation (a JS bundle, typically a library) in another compilation (typically an application).

Usually you perform one Webpack compilation that uses `DllPlugin` and produces two outputs: `bundle.js` and `bundle-manifest.json`. The latter contains a description of all modules contained inside the bundle.

The second step is another compilation that uses `DllReferencePlugin` and the `bundle-manifest.json` from previous step. As usual this compilation produces some `main.js` bundle.

To run your application, you then need to include `bundle.js` _and_ `main.js` in your page.

## Why would I use it?
In the context of an Aurelia application, you would typically split all your vendor libs into a `vendor.js` DLL and then work on your application code only.

A vendor DLL has to following benefits:
- It is compiled only once and then reused, reducing your compilation times. Your app will compile and hot reload much faster.
- It supports long-term caching. If your `vendor.js` seldom changes, browsers can keep it in their cache while updating `main.js` only.

It also has some drawbacks:
- Your build is slightly more complex as you have two different compilations with their own Webpack configuration.
- The resulting files are larger. Because Webpack doesn't know what you're going to actually use in the DLL it has to include everything. Some advanced optimizations are disabled in the DLL, like tree shaking and exports renaming. The main file is also slightly larger because we must map libraries in the DLL with delegated modules.

## How to use it with Aurelia?
There's no much to it and you can see a working example in `demos/05_DllReferencePlugin` in this repo.

### Vendor DLL
In `entry` you must put all the modules from which dependencies are traced and then included in the bundle.
`aurelia-bootstrapper` will get you most of Aurelia core. Option `aureliaConfig` from `AureliaPlugin` applies here.
Be sure to reference optional modules like `aurelia-router` and 3rd party libraries you may use.

You should always have a look at the contents of your main bundle. If a module was not picked in `vendor.js`, add it to the entries! **`aurelia-loader-webpack` must be in your main bundle**. This is taken care of by `AureliaPlugin`, just make sure not to add it explicitly as a vendor entry.

> **Before 2.0 RC3**
> Because your application isn't in this bundle, be sure to pass `aureliaApp: undefined` to `AureliaPlugin`.

In the end, your webpack configuration should be the same as your main one, with the following exceptions:
```js
module.exports = {
  entry: {
    vendor: [
      // Be sure to include all the modules (and their dependencies) that you want to remove 
      // from the main app and put inside the DLL
      "aurelia-bootstrapper",      
      "aurelia-router",
    ],
  },
  output: {
    path: path.resolve("dist"),
    // Slightly different output configuration, at least for library.
    filename: "[name].js",
    library: "[name]_[hash]",
  },
  // ...
  plugins: [
    new DllPlugin({
      path: path.resolve("dist", "[name]-manifest.json"),
      name: "[name]_[hash]",
    }),
    new AureliaPlugin(),
  ],
};
```

You can specify a specific config by running
`webpack --config webpack.config.vendor.js`

> **Before `aurelia-bootstrapper` v2.0.1**
> You need to manually remove `aurelia-loader-webpack` from the manifest JSON file before compiling your main app. Otherwise the application will fail to run with some "module not found" errors, typically your `main` entry point.

### Main bundle
Your main webpack configuration is really the same as usual, except for the inclusion of `DllReferencePlugin`:
```js
module.exports = {
  // ...
  plugins: [
    new DllReferencePlugin({
      manifest: require('./dist/vendor-manifest.json'),
    }),
    new AureliaPlugin(),
  ]
};
```

## Can I use this to bundle my Aurelia lib? 
No, probably not.

Webpack libraries are best distributed in one file per module format -- ideally ES modules using `import/export` even if the code targets ES5, as only ES modules support tree-shaking.

There are two reasons for that:
- as explained above, DLLs prevent several Webpack optimizations and users may value that.
- more importantly, your library probably has dependencies on Aurelia. It means that some Aurelia modules will be embedded into your library DLL. Now imagine another library doing the same thing. At runtime an application would have multiple instances of Aurelia modules and most probably won't work properly.