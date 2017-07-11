---
name: Webpack
---

# Copy static files to webpack build path

If you have static files such as robots.txt file that you want to copy over to the build directory for webpack, I use the [copy-webpack-plugin](https://github.com/kevlened/copy-webpack-plugin).

Pretty simple. In your webpack prod config file:

```
new CopyWebpackPlugin([
  {
    from: path.resolve(__dirname, '../static'),
    to: 'static',
    ignore: ['.*']
  },
  {
    from: path.resolve(__dirname, '../root_static'),
    to: '../dist/',
    ignore: ['.*']
  }
])
```

What this is saying is this:

* All files stores in `/static/` in your website project will be copied to `/dist/static/`.
* All files stores in `/root_static/` in your website project will be copied to `/dist/` (aka: the root of your built website)

You can do much more with this plugin. Check out the [github repo](https://github.com/kevlened/copy-webpack-plugin) to do it. 
