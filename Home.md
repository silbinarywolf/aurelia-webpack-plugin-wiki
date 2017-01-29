This document is a quick introduction to get you started with `aurelia-webpack-plugin` version 2.0.

## Prerequisites
This guide assumes that you have knowledge of building web applications with webpack, nodejs, npm (or yarn) and using the command line. It will not cover those basics but rather focus on how to make Aurelia work along those tools.

## About version 2.0
`aurelia-webpack-plugin` version 2.0 is a complete redesign of the previous Webpack plugins.

As such the plugins have very little in common, so this guide will not help you with version 1.x and _vice-versa_, the docs for 1.x are useless if you use the newer version 2.

## Demos
You can find three minimal working applications in this repository. There is a key difference between demo 1 and demos 2 & 3 which will be explained when we come to it.

## Pre-release
At the time of writing everything has not been merged into Aurelia and published on NPM.
This is why we'll use the following direct Github dependencies inside our `package.json`:
```json
{
  "devDependencies": {
    "aurelia-webpack-plugin": "https://github.com/aurelia/webpack-plugin.git#v2.0",
    "aurelia-core-dependencies": "https://github.com/jods4/aurelia-webpack-build.git#aurelia-core-deps"
  }
}
```
Once everything is properly published, the first one will just be `"aurelia-webpack-plugin": "^2.0.0"` and the second one won't be needed at all.

[[Let's get started!|Webpack config]]