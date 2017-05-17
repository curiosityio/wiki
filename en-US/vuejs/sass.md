---
name: SASS with Vue.js
---

# Get SASS working with Vue.js and Webpack.

* Use the Vue.js CLI program to generate a barebone webpack Vue.js project for you.

* Install SASS webpack loaders:

```
yarn add sass-loader node-sass --dev
```

* Inside of `build/vue-loader.conf.js`, add the following 2 lines:

```
var utils = require('./utils')
var config = require('../config')
var isProduction = process.env.NODE_ENV === 'production'

module.exports = {
  loaders: utils.cssLoaders({
    sourceMap: isProduction
      ? config.build.productionSourceMap
      : config.dev.cssSourceMap,
    extract: isProduction
  }),
  // THESE 2 LINES BELOW:
  scss: 'vue-style-loader!css-loader!sass-loader', // Allows you to use <style lang="scss"> in scoped CSS.
  sass: 'vue-style-loader!css-loader!sass-loader?indentedSyntax' // Allows you to use <style lang="sass"> in scoped CSS.
}
```

Leave everything else. However, you need these 2 lines. These 2 lines allow you to use sass and scss syntax inside of the component styles.

* As far as having external .scss (do *not* use .sass files extension because it's different syntax) files in your code, you just need to add:

```
require('./assets/css/foo.scss')
```

to your `main.js` file to have Webpack import it into your code. At the top of your `foo.scss` file, you can have import statements to import Tachyons and other libs you are interested in using for SASS.
