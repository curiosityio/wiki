---
name: UIViewController
---

# Launch a view controller from another view controller

Say for instance the user logs out of your app and you want to present to them the login view controller to login again.

We take a storyboard by it's filename, and initialize a view controller by it's storyboard ID inside of the storyboard.

* Open your storyboard, open your ViewController you want to present (in this example FooViewController). Where you set the view controller's class name, you will see a property you can set called "Storyboard ID":

![](/docs/images/set_storyboard_id_view_controller.png)

Set the storyboard ID here that we will refer to below:

* In your ViewController code that you want to present the view controller:

```
let fooViewController: FooViewController = UIStoryboard(name: "NameOfStoryboardFileHere", bundle: nil).instantiateViewControllerWithIdentifier("StoryboardIDOfViewControllerHere") as! FooViewController

fooViewController.delegate = self
fooViewController.setFooData(data)

presentViewController(fooViewController, animated: true, completion: nil)
```

If you would like to dismiss the current view controller and then launch the new one, do the following (this is good because if you were to launch the new view controller and then rotate the screen, the old ViewController is still the root and therefore, will be shown to user unless you dismiss it):

```
dismissViewControllerAnimated(false) {
    self.presentViewController(fooViewController, animated: true, completion: nil)
}
```

(`NameOfStoryboardFileHere` would be `Foo` for example if file was named `Foo.storyboard`)

Call presentViewController is where the current view controller presents the new view controller and goes away.

## Launch view controller from another view controller and present it with a navigation bar.

If you want to display a new view controller with it's very own navigation bar which is nice to add "Done" buttons to make the new view controller overlay the existing view controller and then dismiss easily (below is an example):

![](/docs/images/presenet_view_controller_nav_bar.png)

Do the same as above with one little addition:

```
let fooViewController: FooViewController = UIStoryboard(name: "NameOfStoryboardHere", bundle: nil).instantiateViewControllerWithIdentifier("StoryboardIDOfViewControllerHere") as! FooViewController

let navController = UINavigationController.init(rootViewController: fooViewController) // <-- create nav controller from fooViewController

fooViewController.delegate = self
fooViewController.setFooData(data)

presentViewController(navController, animated: true, completion: nil) // <--- present navController instead of fooViewController
```

In your storyboard

# Access view controller inside of UIContainer

If you have a container, you can access the view controller inside of the container:

* In the embed segue setup connecting your container to view controller in storyboard, set the identifier to something (I usually set it to the name of the destination view controller name). We will be using this segue name below in the parent view controller code. Also, make sure that the embed segue destination view controller is set.

* Parent view controller code:

```
class CustomViewController: UIViewController {
    func myMethod() {}
}

class MainViewController: UIViewController {

    private var embeddedViewController: CustomViewController!

    override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        if let vc = segue.destinationViewController as? CustomViewController
            where segue.identifier == "EmbedSegue" {

            self.embeddedViewController = vc
        }
    }

    override func viewDidAppear(animated: Bool) {
        self.embeddedViewController.myMethod()
    }
}
```

Notice the segue.identifier name set in the code above. This needs to be the name that you set in the previous step.

Thank you [reference doc](http://stackoverflow.com/a/29582305/1486374)

---

Above gives a great way for the parent to learn about the child. For code in the child to reach the parent, use `self.parentViewController` from child view controller.

# Present login screen to user if not logged in, then present completely different view (such as a left side menu and/or navigation controller) when user logs in.

This is a common task. You have the main part of your app which has a navigation controller maybe even a slide out left navigation menu for navigation purposes. You don't want this view to be in your login screen.

The secret is to use AppDelegate's `window` variable. The `window` controls what is shown to the user. `window` has a property `rootViewController`. `rootViewController` is the view controller shown to the user. We will be using this property to toggle between login and main view of our app.

* When app first launches, check if the user is logged in or not.

```
import UIKit
import SlideMenuControllerSwift

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, SignInViewControllerDelegate {

    var sideMenuController: SlideMenuController?

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        ...

        if isUserAlreadyLoggedIn() {
            // go to main part of app with navigation controller, side menu, whatever.
        } else {
            // go to login screen of app.
        }

        return true
    }

    func isUserAlreadyLoggedIn() -> Bool {
        return UserManager.isUserLoggedIn() // <-- do whatever you need to to determine if the user has logged in or not.
    }

    ...    

}
```

When the app first launches, `func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool` is called in the AppDelegate. Great place to decide what view to show depending on if the user has logged in or not.

* Launch the main part of screen or launch the login screen. In this example, I am going to show a left and right side menu to the navigation of the "main" part of the app after the user logs in.

The secret is to launch the appropriate screen when app launches, then set the delegate of SignInViewController to AppDelegate. We do this because AppDelegate is the only one with access to `window` which is going to determine what screen is shown to the user.

```
import UIKit
import SlideMenuControllerSwift

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, SignInViewControllerDelegate {

    var sideMenuController: SlideMenuController?

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        ...

        if isUserAlreadyLoggedIn() {
            goToMainPartOfApp()
        } else {
            goToLoginPartOfApp()
        }

        return true
    }

    private func goToMainPartOfApp() {
        let mainViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("MainScreenId")
        let leftViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("LeftSideMenuId")
        let rightViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewControllerWithIdentifier("RightSideMenuId")

        sideMenuController = SlideMenuController(mainViewController: mainViewController, leftMenuViewController: leftViewController, rightMenuViewController: rightViewController)
        self.window?.rootViewController = sideMenuController  <--- setting the rootViewController changes what screen is shown to user.
        self.window?.makeKeyAndVisible()  <---- not sure exactly what this does. Docs say it is "convenient" to use.
    }

    func userLoggedInSuccessfully() {
        goToMainPartOfApp()
    }

    private func goToLoginPartOfApp() {
        let loginViewController = UIStoryboard(name: "Login", bundle: nil).instantiateViewControllerWithIdentifier("SignInViewControllerId") as! SignInViewController // swiftlint:disable:this force_cast

        loginViewController.delegate = self <--- set delegate of SignInViewController

        self.window?.rootViewController = loginViewController <--- setting the rootViewController changes what screen is shown to user.
    }

    func isUserAlreadyLoggedIn() -> Bool {
        return UserManager.isUserLoggedIn()
    }

    ...    

}
```

There are [methods of animating the transition](https://stackoverflow.com/questions/8827891/swapping-rootviewcontroller-with-animation) between rootViewControllers as well. 
