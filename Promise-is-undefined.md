Aurelia uses javascript `Promise` a lot and assumes it's present in the browser. 
It's available pretty much everywhere, except in legacy browsers such as IE11 and lower.

By default Aurelia provides polyfills for all features that it needs inside `aurelia-polyfills` (which is automatically included), _with the notable exception of `Promise`._ One reason is that some people have strong preferences for Promise libraries, which sometimes come with a lot of additional features, for example Bluebird, RSVP or others.

One way to fix errors is to use `es6-promise-promise`, which is a very slim `Promise` polyfill, with no features added.
```js
plugins: [
  new ProvidePlugin({
    Promise: 'es6-promise-promise'
  })
]
```
What `ProvidePlugin` does is that each time a module references a global variable `Promise`, it injects an `import * as Promise from "es6-promise-promise"`, so that the `Promise` implementation comes from the polyfill.

You can use other polyfills for `Promise`, look up their documentation for how to include them.