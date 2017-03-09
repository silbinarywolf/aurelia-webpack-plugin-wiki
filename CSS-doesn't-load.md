It is a common problem not being able to load CSS, although it's not directly linked to `AureliaPlugin`.

If your CSS file was found by Webpack during compilation and is properly included in your bundle, but you can get some errors at runtime when trying to load it, it is very likely an improper Webpack loaders configuration. For example, when trying to use `<require>` to load CSS in a view:
```
Failed loading required CSS file: module/file.css.
```

Let's review the relevant loaders here:
- `less-loader` and co. Those transform your LESS (or SASS, etc.) file into pure CSS content. They go first if you use some preprocessor.
- `css-loader` wraps your CSS into a JS string, that can be loaded at runtime.
- `style-loader` wraps the CSS string produced in previous step with a JS function that injects it into the DOM in a `<style>` tag and supports hot reload. You might want this _or not_ depending on how you intent on loading the CSS at runtime.

There are at least three different ways to import CSS at runtime:
- With a plain `<link>` tag and a real `.css` file. If you want to use Webpack to produce this you usually use the `ExtractTextWebpackPlugin`. We won't go into more details here, please refer to [its documentation](https://github.com/webpack-contrib/extract-text-webpack-plugin).
- With an ES `import "style.css"` in your javascript. _You'll need both_ `css-loader` and `style-loader` (in reverse order).
- With an Aurelia `<require from="style.css">` in the view. In this case, Aurelia already provides the function to insert the CSS in DOM and expects _just CSS code_. You need _only_ `css-loader`.

Note that the `<require>` option has some differences compared to `import`:
- Both support HMR;
- support is currently kind of hardcoded to `.css` in `aurelia-loader-webpack` and `aurelia-templating-resources`. It won't work with `.less` or `.sass` out of the box.
- `<require>` supports isolated styles in shadow DOM (currently Chrome only) and scoped styles (deprecated, Firefox only).

Here's an example configuration, that cleverly lets you mix `<require>` and `import` in the same project by applying loaders _based on the file that requested the stylesheet!_

```js
module: {
  rules: [
    {
      test: /\.css$/i,
      loader: 'css-loader',
      issuer: /\.html?$/i
    },
    {
      test: /\.css$/i,
      loader: ['style-loader', 'css-loader'],
      issuer: /\.[tj]s$/i
    }
  ]
}
```