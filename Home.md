This wiki contains various information about the new (v2.0) Aurelia plugins for Webpack.

## Getting started
A good starting point is reading the [Getting started](Getting-started) documentation.

This repo also contains various demos projects that build and run.

## Reference
[All `AureliaPlugin` options](AureliaPlugin-options)

[Other plugins that might be useful](Secondary-plugins)

## Advanced topics
[Using Webpack DLL](Using-Webpack-DLL)

[Minimize size](Minimize-size)

## Troubleshooting
[Some modules are not found at runtime](Debugging-missing-modules)

[HtmlWebpackPlugin problems](HtmlWebpackPlugin)

[CSS doesn't load](CSS-doesn't-load)

[Promise is undefined](Promise-is-undefined)

When migrating projects that used previous versions of Aurelia and `aurelia-webpack-plugin`, NPM dependencies can sometimes be hard to update consistently. 
- Be sure that **you don't use `aurelia-bootstrapper-webpack`**. Always use `aurelia-bootstrapper`.
- Be sure that you have latest versions of Aurelia and Webpack. 
- You don't need to take a direct dependency on `aurelia-loader-webpack`, `aurelia-webpack-plugin` takes care of that for you.
- If things inexplicably don't work as they are supposed to, try to completely delete your compilation output and your `node_modules`, then install anew.

That last advice may seem random and ineffective but we've seen several people mysteriously fixing their issues by completely reinstalling `node_modules` from scratch. NPM can get messy at times.
