---
name: Notification Center
---

Notification center is an event bus for iOS.

# Post new event

```
static let NOTIFICATION_USER_LOGGED_OFF = "NOTIFICATION_USER_LOGGED_OFF"

class func postUserLoggedOff() {
    postNotification(NOTIFICATION_USER_LOGGED_OFF)
}

private class func postNotification(name: String) {
    NSNotificationCenter.defaultCenter().postNotificationName(name, object: nil)
}
```

# Subscribe to an event

```
class func observeUserLoggedOff(observer: AnyObject, selector: Selector) {
    observeNotification(observer, selector: selector, name: NOTIFICATION_USER_LOGGED_OFF)
}

private class func observeNotification(observer: AnyObject, selector: Selector, name: String) {
    NSNotificationCenter.defaultCenter().addObserver(observer, selector: selector, name: name, object: nil)
}
```

Then in some class that wants to observe:

```
NotificationCenterUtil.observeUserLoggedOff(self, selector: #selector(FooViewController.userLoggedOff(_:)))
```
