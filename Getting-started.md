This document is a quick introduction to get you started with `aurelia-webpack-plugin` version 2.0.

## Prerequisites
This guide assumes that you have knowledge of building web applications with webpack, nodejs, npm (or yarn) and using the command line. It will not cover those basics but rather focus on how to make Aurelia work along those tools.

To get everything working you'll need recent releases:
- `aurelia-bootstrapper@2.1.1`
- `webpack@2.2.1`

## What's the problem with Aurelia anyway?
Webpack is a wonderful tool. It transforms and bundles every file that your web application uses.

The first step in that build process is to find out all the required files, by detecting what uses what, which webpack names _Dependencies_. Webpack is quite smart about that and recognizes many kind of dependencies like ES `import`, CommonJS `require`, AMD and more.

The problem with Aurelia is that a lot of modules are dynamically loaded at runtime by `aurelia-loader`. For example, your router configuration might indicate `moduleId: 'home'`, which will be dynamically loaded when the user actually navigates to that route. But webpack doesn't know about those strings. It doesn't know that they are dependencies that need to be bundled with the rest.

Inside a bundle, Webpack also renames the modules to sequential integers. This is shorter than full names and hence more efficient. The second problem is that when `aurelia-loader` tries to load your `'home'` module, Webpack has no idea what this module is because it erased its name.

What `AureliaPlugin` does basically comes down to those two issues:
- It understands Aurelia code and adds Webpack dependencies for anything that is dynamically loaded with `aurelia-loader`.
- It normalizes and preserves the name of those modules so that they can be found at runtime.

## About version 2.0
`aurelia-webpack-plugin` version 2.0 is a complete redesign of the previous Webpack plugins.

As such the plugins have very little in common, so this guide will not help you with version 1.x and _vice-versa_, the docs for 1.x are useless if you use the newer version 2.

## Demos
You can find several minimal working applications in [this repository](https://github.com/jods4/aurelia-webpack-build).

[[Let's get started!|Webpack config]]