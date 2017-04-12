---
name: Patterns
---

# Check if user is already logged into app and launch initial ViewController.

In AppDelegate:

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    if isUserAlreadyLoggedIn() {
        goToMainPartOfApp()
    } else {
        goToLoginPartOfApp()
    }    

    return true
}

func isUserAlreadyLoggedIn() -> Bool {
    return // is user logged in? Look up value in NSUserDefaults for example.
}

fileprivate func goToLoginPartOfApp() {
    let loginViewController = UIStoryboard(name: "Login", bundle: nil).instantiateViewController(withIdentifier: "SignInViewControllerId") as! SignInViewController // swiftlint:disable:this force_cast

    loginViewController.delegate = self // Usually used so when user is logged in, you can call goToMainPartOfApp() from delegate.

    self.window?.rootViewController = loginViewController
}

fileprivate func goToMainPartOfApp() {
    let mainViewController: FooViewController = UIStoryboard(name: "Main", bundle: nil).instantiateViewController(withIdentifier: "FooViewControllerId") as! FooViewController // swiftlint:disable:this force_cast

    let navigationController = getNavigationController(mainViewController)

    self.window?.rootViewController = navigationController
    self.window?.makeKeyAndVisible()
}

fileprivate func getNavigationController(_ viewControllerToEmbed: UIViewController) -> UINavigationController {
    let navigationController = UINavigationController()
    navigationController.viewControllers = [viewControllerToEmbed]

    navigationController.navigationBar.barTintColor = AppConstants.primaryColor
    navigationController.navigationBar.tintColor = UIColor.white
    navigationController.navigationBar.isTranslucent = false

    let titleDict: NSDictionary = [NSForegroundColorAttributeName: UIColor.white]
    navigationController.navigationBar.titleTextAttributes = titleDict as? [String : AnyObject]        

    return navigationController
}
```
