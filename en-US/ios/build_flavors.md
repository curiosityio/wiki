---
name: Build flavors
---

# Create build flavors for app

If you white label an app and want to create different versions of your app with same code base, this is how you do so.

You have a fast food app you created on how to order food. You want to create a Subway version, Wendy,s version, Burger King version. This is what you would do.

* Create build targets

You will want to create a new build target for each build flavor you want. Create a Subway one, Wendy's one, Burget King one. You do so using the guide in the wiki for [creating debug and prod build targets](/docs/ios/debug_prod#create-dev%2C-beta%2C-and-release-build-types). Make sure to duplicate the 'Release' build type.

* Configure app with new build target

After you create all of your build targets for all the flavors (Subway, Wendy's, etc) you want, you need to configure them each in the build settings. Open up your Target project settings > Build Settings.

Search and edit each of the following:

1. Product Bundle Identifier.
2. Product Name.

* Create user defined variable for build flavors

Now, you can make builds of your Subway app and the name as well as package name will be correct. However, all the code at runtime is still the same. We need to change that so at runtime we run different code according to what build flavor we are running.

While in your Target project settings > Build Settings, you need to create a new set of "User-Defined" variables (they are all found if you scroll to bottom of Build Settings screen).

![](/docs/images/add_user_defined_set.png)

Name it "BUILD_FLAVOR". Then under each, name them all accordingly. Name the Subway build target as "Subway", Wendy build target as "Wendys", Burger King build target as "BurgerKing" all for example.

Now, go into your Info.plist file and add this line:

![](/docs/images/info_plist_build_flavor.png)

Next step we will use this value we just created.

* Each build flavor use different app launch icon

Create app icon sets in your image assets folder for each build flavor. I use [fastlane app icon plugin generator](https://github.com/KrauseFx/fastlane-plugin-appicon) to generate icon sets for me. Create `AppIconSubway.appiconset`, `AppIconWendy.appiconset`, `AppIconBurgerKing.appiconset` for example.

After generating these app icon sets, go into your project settings > Build Settings. Search "asset cat" and you should see this:

![](/docs/images/asset_catalog_ios.png)
> thanks to [this blog post](https://engineering.circle.com/different-app-icons-for-your-ios-beta-dev-and-release-builds-af4d209cdbfd) for the photo creds.

For each build flavor in there, enter the name of the app icon set for each. `AppIconSubway`, `AppIconWendy`, `AppIconBurgerKing` (don't add `.appiconset` in the name).

* Run different code depending on build flavor

Create a new file name `BuildFlavorManager`. Here is the code for the Swift visitor pattern you will need to create:

```
class BuildFlavorManager {

    class func getFlavor() -> BuildFlavor {
        let buildFlavor = Bundle.main.infoDictionary!["BUILD_FLAVOR"] as! String

        switch buildFlavor {
        case "Subway":
            return SubwayBuildFlavor()
        case "Wendys":
            return GemsOfHopeBuildFlavor()
        default:
            return SubwayBuildFlavor()
        }
    }

}

protocol BuildFlavorVisitor {
    func visit(flavor: SubwayBuildFlavor)
    func visit(flavor: WendysBuildFlavor)
}

protocol BuildFlavor {
    func accept(visitor: BuildFlavorVisitor)
}

class SubwayBuildFlavor: BuildFlavor {
    func accept(visitor: BuildFlavorVisitor) { visitor.visit(flavor: self) }
}

class WendysBuildFlavor: BuildFlavor {
    func accept(visitor: BuildFlavorVisitor) { visitor.visit(flavor: self) }
}
```

Now, we finally get to the part where we run different code at runtime.

Let's say we want to have different primary colors used in the app depending on the build flavor. This is how you would do so:

```
class AppConstants {

    class PrimaryColorVisitor: BuildFlavorVisitor {
        var color: UIColor? = nil

        func visit(flavor: SubwayBuildFlavor)  { color = UIColor(hexString: "#333333") }
        func visit(flavor: WendysBuildFlavor) { color = UIColor(hexString: "#666666") }
    }    

    static var primaryColor: UIColor {
        get {
            let buildFlavor = BuildFlavorManager.getFlavor()
            let visitor = PrimaryColorVisitor()
            buildFlavor.accept(visitor: visitor)
            return visitor.color!
        }
    }    

}
```

Anytime that you need to get the primary color now, call `AppConstants.primaryColor` to get it dynamically at runtime.
