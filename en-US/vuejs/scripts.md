---
name: Vuejs scripts
---

# Include remote javascript file in Vuejs project

If you are trying for instance to implement Stripe into your Vuejs app, Stripe requires that you include the following script in your index.html page to include globally across your site:

```
<script src="https://js.stripe.com/v3/"></script>
```

Then in your Vuejs component you need to have some code such as:

```
const stripe = Stripe('pk_test_555555555555555555')
const elements = stripe.elements()
```

But when you go to compile your Vuejs app, eslint will have a fit saying that Stripe is not found. Here is how to fix this.

* In your `index.html` file in your vuejs application, include your Stripe js script file:

```
<!DOCTYPE html>
<html>
  <head>
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <meta name='theme-color' content='#F8F8FB'>

      <script src="https://js.stripe.com/v3/"></script> <!-- here you go -->

    <meta charset="utf-8">
  </head>
  <body class="bg-white near-black center">
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

* Then, in your `.eslintrc.js` file, include the following:

```
// http://eslint.org/docs/user-guide/configuring
module.exports = {
  ...
  'globals': {
      "Stripe": true
  },
  ...
}
```

Now, eslint will continue on with compiling and Stripe *will* be round when the app is loaded. Thank you to [this comment](https://github.com/vuejs/vue-loader/issues/173#issuecomment-209345369) that took care of solving this for me. 
