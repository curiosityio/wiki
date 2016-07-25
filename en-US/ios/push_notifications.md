---
name: Push notifications
---

# Adding remote push notifications functionality to app.

There are 2 different types of notifications for iOS. Remote and local notifications. Remote push notifications are notifications received from the Apple Push Notification service (APN). Local notifications are the ones that the user receives on their device in the lock screen telling them there is an update to the app. You can make notifications with sound, vibrations, text, pictures, etc.

You need to ask the user for permission to allow both of these permissions (don't have to ask for both but usually apps use both). After you ask, you can get the "device token" and send that up to your application's rest API which will then create a AWS SNS endpoint which allows it to receive messages.

* Add code to app delegate to ask for these permissions:

```
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
    ...

    askUserForNotificationsPermission(application)

    return true
}

func askUserForNotificationsPermission(application: UIApplication) {
    application.registerUserNotificationSettings(UIUserNotificationSettings(forTypes:[.Sound, .Alert, .Badge], categories: nil))
}

func application(application: UIApplication, didRegisterUserNotificationSettings notificationSettings: UIUserNotificationSettings) {
    if notificationSettings.types != .None { // check if user allows us to send notifications. If so, setup remote push notifications.
        application.registerForRemoteNotifications()
    }
}

func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
    let tokenChars = UnsafePointer<CChar>(deviceToken.bytes)
    var tokenString = ""

    for i in 0..<deviceToken.length {
        tokenString += String(format: "%02.2hhx", arguments: [tokenChars[i]])
    }

    // tokenString now ready to send up to rest API to create an AWS endpoint and send notification.
}

func application(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSError!) {    
    // can't really do anything with this error except maybe logging it in your crash reporting tool to keep track of it?
    println(error.localizedDescription)
}
```

Edit the `.Sound`, `.Alert`, `.Badge` to use the permissions you are looking to use.

```
.Badge allows the app to display a number on the corner of the app’s icon.
.Sound allows the app to play a sound.
.Alert allows the app to display text.
```

Or use `.None`

* That's it. When your app is in the background, iOS will automatically take care of receiving the notification and showing a local notification for the remote message. When your app is in the foreground, it will not show any sort of local notification for the remote message. However, there is a callback function in the app delegate you can use

Thanks to [this gist](https://gist.github.com/sawapi/a7cee65e4ad95578044d) for this help. Also to [this tutorial](https://www.raywenderlich.com/123862/push-notifications-tutorial) giving some good details as well (I also downloaded a local copy of this doc in misc-docs).
