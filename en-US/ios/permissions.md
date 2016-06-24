---
name: Permissions
---

# Ask for permission to show push notifications

In your app delegate file:

```
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
    askUserForNotificationsPermission(application)

    return true
}

func askUserForNotificationsPermission(application: UIApplication) {
    if UIApplication.instancesRespondToSelector(#selector(UIApplication.registerUserNotificationSettings(_:))) {
        application.registerUserNotificationSettings(UIUserNotificationSettings(forTypes:[.Sound, .Alert, .Badge], categories: nil))
    }
}
```

You may add or more any of the `.Sound, .Alert, .Badge` types according to what your app wants to do. Or use `.None` to not show any UI.

You don't have to ask in the app delegate (although I have not tested it) you may ask in a view controller and obtain an application instance and use that.

To check if a user has allowed or denied push notifications:

```
static func areAppNotificationsEnabled() -> Bool {
    let notificationType = UIApplication.sharedApplication().currentUserNotificationSettings()!.types

    return notificationType != UIUserNotificationType.None
}
```
