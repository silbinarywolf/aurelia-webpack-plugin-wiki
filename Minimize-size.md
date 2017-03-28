We all know size matters. The smaller your application, the better.

This page contains a few tips that may help you reduce the size of your package.
`04_Small_ES6_build` is the companion project.

## Use ES6 (if you can)
Generally speaking ES6 code is more compact than equivalent ES5, for example thanks to `=>` instead of `function`, object literal shorthands `{ x, y }`, `class` syntax and more.

Transpiling ES6 code to ES5 is even worse as some simple syntax can turn into a lot of downlevel code, like `for..of` or `async/await`.

_If you only support ES6 browsers,_ i.e. no IE, you should:
- code in ES6 javascript;
- use `target: 'es6'` in your `tsconfig.json` if you use Typescript;
- pass `dist: 'es2015'` to `AureliaPlugin` so that it uses ES6 libraries.

## Minify your code
This is really a no brainer. Minifying javascript code removes all comments, non-significant spaces, renames your variables to one letter identifiers and more. It makes a _huge_ different in code size.

Because the resulting javascript is unreadable, you usually want to do that only for production, and/or use sourcemaps.

A pitfall here is that as of today UglifyJS (Webpack's default minifier) doesn't support ES6. So if you followed the previous tip and publish ES6, you need another minifier. Babili is a good option.

- On the command line `webpack --optimize-minimize` minifies your bundle. 
- In your webpack config, using `UglifyJsPlugin` has the same effect.
- If you publish ES6, use `BabiliPlugin` instead. [This project](https://github.com/jods4/aurelia-webpack-build/tree/master/demos/04-Small_ES6_build) shows you how.

## Serve gzipped contents
Even when minified, javascript code contains lots of redundant elements like `function` and common characters like `=`.

All browsers support downloading gzipped files. This compression can typically reduce the size of your script on the wire to a third of their uncompressed size.
Some browsers support alternate compression methods like Brotli, which may give even better compression ratios.

This step is not related to the Webpack build (maybe unless you want pre-compressed `.gz` files).
- Be sure that your web server is properly configured to serve compressed (gzip) files.

## Include only core modules that you use
When configuring Aurelia, it's common to call `aurelia.use.standardConfiguration()`. 
This includes the following modules, some of which you may not actually need: 
`router`, `history`, `eventAggregator`, `developmentLogging` (logging is a separate call).

By default, `AureliaPlugin` assumes you'll use everything as well and adds all the relevant dependencies.

_If you build a small page or simple app that doesn't use all of them_:
- don't call `standardConfiguration()`, there are other methods for each module that you actually use.
- reflect that configuration by passing [`aureliaConfig`](https://github.com/aurelia/webpack-plugin/wiki/AureliaPlugin-options#aureliaconfig) to `AureliaPlugin`.

## Remove seldom used features
Aurelia contains a few feature that are rarely used, for example binding SVG properties (20K of minified code).

Another large example is polyfills. Aurelia comes with the minimal set of polyfills so that it just works on browsers up to IE9. 
If the browsers you support are all ES5 or even ES6 compliant, you can reduce the size of the polyfills. 
Another common scenario is that you already include your own polyfills (e.g. based on `core-js`) and Aurelia's own polyfills are redundant, in which case they can be totally removed (saves around 10K of minified code).

_If you don't use some features_, when building with Webpack you can drop the related code:
- Use the [features](https://github.com/aurelia/webpack-plugin/wiki/AureliaPlugin-options#features) `AureliaPlugin` option to remove unused features.