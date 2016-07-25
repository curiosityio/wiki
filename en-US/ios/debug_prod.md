---
name: Debug and Prod build versions
---

# Change code according to debug or release

```
#if !DEBUG
    static let hostname: String = "https://api.curiosityio.com"
#else
    static let hostname: String = "https://staging.curiosityio.com"
#endif
```

This works because it is a XCode preprocessor macro. This will not work by default. You must add the custom flag to the Swift compiler custom flags in your Build Settings.
Go to  Build Settings -> Swift Compiler - Custom Flags then add `-DDEBUG` as an entry for Debug:

![](/docs/images/swift_custom_debug_flag.png)

[Original help](http://stackoverflow.com/a/24112024/1486374)

# Create dev, beta, and release build types

*Note: The below instructions were originally from [this guide](https://engineering.circle.com/different-app-icons-for-your-ios-beta-dev-and-release-builds-af4d209cdbfd#.r7qb5bxio) with me editing some parts of it. I found some issues with the guide so needed to write my own edited version.*

By default, XCode creates dev and release build types for your app. Adding a beta build type is handy to run beta tests with the latest and greatest before it hits release.

This is how to add this beta build type to XCode:

* In Xcode, click on your project, and click on your project on the top of the left pane. Be sure the project is selected in the top left — not your Target.
On the Info tab, click the “+” Sign under “Configurations” and select “Duplicate ‘Release’ Configruation”
Name this configuration “Beta”

![](/docs/images/xcode_add_beta_to_project.png)

As of now, you’ll be able to build Debug, Beta, and Release builds. But they all have the same app name and bundle ID. Let’s fix that.

* While you’re in your project settings, select your Target from the left sidebar and go to the “Build Settings” tab.
In the “Packaging” section of your Build Settings, expand the “Product Name” field. (If you can’t find it, just search in the top right bar)
In each of the configurations, give your app a new name. This names the app on the home screen when app is installed.

![](/docs/images/xcode_add_beta_product_name.png)

* Stay in the same section you are in with naming the product name. We are now editing the bundle ID so you can have all 3 builds on your same device and none of them will override each other.

Still under the Packaging settings, edit the bundle id section by adding ".beta" and ".dev" to the Beta and Dev builds.

![](/docs/images/xcode_add_beta_bundle_id.png)

*Note: You can follow [this guide](https://engineering.circle.com/different-app-icons-for-your-ios-beta-dev-and-release-builds-af4d209cdbfd#.r7qb5bxio) to change the bundle ID with a user specified variable set. However, I have found that when adding entitlements to your app such as push notifications, this method explained here is the only way that works.*

* Here is where I usually stop. However, you can create different icons for each type of build by [following this guide](https://engineering.circle.com/different-app-icons-for-your-ios-beta-dev-and-release-builds-af4d209cdbfd#.r7qb5bxio)
