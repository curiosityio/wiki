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

    func crashlyticsDidDetectReportForLastExecution(report: CLSReport, completionHandler: (Bool) -> Void) {  <------- add this function. This will make it so app crashes only get submitted to Fabric on non-debug builds.
        #if !DEBUG
            let submitReport = true
        #else
            let submitReport = false
        #endif

        NSOperationQueue.mainQueue().addOperationWithBlock {
            completionHandler(submitReport)
        }
    }
}
```
