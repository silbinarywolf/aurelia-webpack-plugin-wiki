Aurelia comes with a minimal set of polyfills that it requires to work, inside `aurelia-polyfills`.
You don't need to import this package explicitely as `aurelia-bootstrapper` imports it for you.

## Removing polyfills
Maybe you are lucky to target modern browsers only
or maybe you import your own set of more complete polyfills.

In those situations, you might want to remove Aurelia's own polyfills from your build.
This can reduce the size of your bundle and avoid conflicts between different polyfills.

The plugin [`features`](https://github.com/aurelia/webpack-plugin/wiki/AureliaPlugin-options#features) option enables you to remove polyfills from the build output, with varying levels of compliance: ES2015, ES2016, ESnext (for `Reflect.metadata` and co).

## Internet Explorer
To reduce the size of `aurelia-polyfills` Aurelia does not include commonly implemented features that are missing from Internet Explorer.

If you want to support Internet Explorer, you have to include your own polyfills for the following features:
- `Promise` is missing from IE9-11.
- `MutationObserver` is missing from IE9-10.
- `requestAnimationFrame` is missing from IE9.

## Including custom polyfills
If you include your own polyfills instead of -- or in addition to (IE) -- Aurelia's, you need to make sure that they are loaded before any Aurelia module is imported.
You should also make sure that they are installed globally as a native implementation would, or maybe try to use Webpack `ProvidePlugin`.

Good ways to load your polyfills before anything else include:
- Putting them in a separate `<script>` tag before your bundle script.
- **Starting with 2.0 RC3** Putting them in your Webpack entry configuration, before `aurelia-bootstrapper`.

## Example
Let's say we target IE11, so we need to add a `Promise` polyfill to run properly.

There are many `Promise` polyfills available, we'll use `es6-promise`. 
First step is to add it to our dependencies, e.g. `yarn add es6-promise`, with npm or manually.

Second step is to add it to `webpack.config.js`:
```js
module.exports = {
  entry: ["es6-promise/auto", "aurelia-bootstrapper"],
  // ...
};
```
And _voilà_ it works!

Notice how we imported `es6-promise/auto`, because according to its documentation this is how we define a global `Promise`. Importing `es6-promise` doesn't change the global scope, it only returns a `Promise` implementation for local usage (see _Ponyfills_).