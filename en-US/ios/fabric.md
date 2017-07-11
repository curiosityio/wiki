---
name: Fabric.io
---

# Install Fabric into iOS project

* Install the Fabric Mac app to your computer.

* In the Mac app, sign in to your account, in the top left corner you will see a little drop down arrow:

![](/docs/images/fabric_mac_app_dropdown.png)

Press it and it will reveal a button on the top right side "+ New App":

![](/docs/images/fabric_new_app.png)

It will walk you through adding a new app.

* Eventually, it will ask you to drag and drop a briefcase looking icon into your project. It also notes to uncheck the checkbox that says something about copying. I keep it checked. I like to copy over the framework code into my projects to help make it easier to install on other's machines.

* Then, the Fabric app will give you code snippets to install into your code base. I don't follow those directions. I do my own because I don't like Fabric to submit code crashes on debug builds. This is how to get around that:

```
import Fabric      <----- import the frameworks
import Crashlytics <-----

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, CrashlyticsDelegate {  <---- Add CrashlyticsDelegate here.

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        Crashlytics.sharedInstance().delegate = self <-------- these 2 lines need added to code.
        Fabric.with([Crashlytics.self])              <--------    

        ...

        return true
    }

    func crashlyticsDidDetectReport(forLastExecution report: CLSReport, completionHandler: @escaping (Bool) -> Void) {
        #if !DEBUG
            let submitReport = true
        #else
            let submitReport = false
        #endif

        OperationQueue.main.addOperation {
            completionHandler(submitReport)
        }
    }
}
```

# Log errors to crashlytics

Here is a handy util class to log to crashlytics or to console depending on debug builds or not.

```
import CocoaLumberjack

class LogUtil {

    class func log(_ message: String?) {
        var logMessage = "Log message is nil......."

        if let message = message {
            logMessage = message
        }

        DDLogDebug(logMessage)
    }

    class func logArgs(_ message: String, args: CVarArg...) {
        DDLogDebug(String(format: message, arguments: args))
    }

    class func logError(_ error: Error) {
        #if DEBUG
            DDLogDebug(String(format: "ERROR THROWN: %@", error.localizedDescription))
        #else
            Crashlytics.sharedInstance().recordError(error, withAdditionalUserInfo: ["description": error.localizedDescription])
        #endif
    }

}
```
