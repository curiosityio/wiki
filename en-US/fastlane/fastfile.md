---
name: Fastfile
---

# Run other fastlane lanes from another lane in Fastlane file

[Official doc where I got this](https://github.com/fastlane/fastlane/blob/master/fastlane/docs/Advanced.md#switching-lanes)

Let's say you have a generic fastlane lane for creating a build of your iOS mobile app. This code may be in your `Fastlane` file:

```
desc "Create new build of mobile app"
lane :build_app do |values|
  gym(scheme: 'Fastfood app', configuration: 'Subway', export_method: 'ad-hoc')  
end
```

From the code above, the scheme of our XCode codebase is `Fastfood app` with a configuration called `Subway`. This is great for whitelabing mobile apps.

What if we want to create a build of our same app but with the configuration of Burger King? We don't want to copy/paste that's just a waste. We want to reuse this lane we already created. Easy:

```
desc "Create Subway build of app"
lane :build_subway do |values|
    build_app(configuration: 'Subway')
end

desc "Create Burger King build of app"
lane :build_subway do |values|
    build_app(configuration: 'Burger King')
end

desc "Create new build of mobile app"
lane :build_app do |values|
  configuration = values[:configuration]
  gym(scheme: 'Fastfood app', configuration: configuration, export_method: 'ad-hoc')  
end
```

We can use some simple Ruby code in our Fastlane file to pass dynamic variables to our generic `build_app` function. We can reuse this lane now.
