`html-webpack-plugin` is a package that helps you create your `index.html` starting page for production.
It can automatically insert all the required scripts, with their hashes for proper long-term caching, perform some templating and more. A great tool. 
To learn more about it you should read [their own documentation](https://github.com/jantimon/html-webpack-plugin).

When trying to use the plugin, you might run into errors that your `index.html` contains invalid tokens / does not parse correctly.
Or alternatively, the resulting HTML file is the same as input and templates did not properly substitue.

The reason for that is that your template is compiled by Webpack and its loaders are incorrectly configured.

`HtmlWebpackPlugin` will use a default loader that includes [EJS templating](http://www.embeddedjs.com), _but only if no loader at all is applied_ ([relevant docs](https://github.com/jantimon/html-webpack-plugin/blob/master/docs/template-option.md)).
The catch is that `AureliaPlugin` inserts `html-resources-loader` for all `.html` files by default. This loader detects dependencies such as `<require>` tags.
Because of that loader, `HtmlWebpackPlugin` does not add its own loader and things don't work well.

There are at least three ways to fix the problem. From simple to complex:

1. Use a different file extension like `index.ejs`. That way no template applies and `HtmlWebpackPlugin` does its magic. This seems appropriate as the file is a template, not an actual HTML file.

2. Specify the loader explicitly, prefixing with `!` (which disables all default loaders). For example if you want the default EJS templating (and only that): `{ template: '!html-webpack-plugin/lib/loader!index.html' }`

3. Pass `noHtmlLoader: true` to `AureliaPlugin`. You are then fully responsible to manage your loaders yourself. In particular, you'll want to add a `rule` with `html-resources-loader` in the appropriate place.