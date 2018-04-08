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
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        ...

        // Make sure fabric is last.
        #if !DEBUG
            Fabric.with([Crashlytics.self])
            DDLog.add(CrashlyticsCocoaLumberjackLogger.sharedInstance)
        #endif        

        return true
    }
}
```

# Log errors to crashlytics

Use CocoaLumberjack to log all of your debug statements and exceptions. Then depending on if you're running in debug or release, it will log to the console or to Crashlytics.

Create this file in your code:

```
//
//  CrashlyticsCocoaLumberjackLogger.swift
//
//  Created by Levi Bostian on 10/30/17.
//  Copyright © 2017 Curiosity IO. All rights reserved.
//
import Foundation
import CocoaLumberjack
import Crashlytics

class DefaultCrashlyticsLogFormatter: NSObject, DDLogFormatter {
    func format(message logMessage: DDLogMessage) -> String? {
        // Formatter found: https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Framework/iOSSwift/Formatter.swift
        let threadUnsafeDateFormatter = DateFormatter()
        threadUnsafeDateFormatter.formatterBehavior = .behavior10_4
        threadUnsafeDateFormatter.dateFormat = "HH:mm:ss.SSS"
        let dateAndTime = threadUnsafeDateFormatter.string(from: logMessage.timestamp)

        var logLevel: String
        let logFlag = logMessage.flag
        if logFlag.contains(.error) {
            logLevel = "E"
        } else if logFlag.contains(.warning) {
            logLevel = "W"
        } else if logFlag.contains(.info) {
            logLevel = "I"
        } else if logFlag.contains(.debug) {
            logLevel = "D"
        } else if logFlag.contains(.verbose) {
            logLevel = "V"
        } else {
            logLevel = "?"
        }

        let formattedLog = "\(dateAndTime) |\(logLevel)| [\(logMessage.fileName) \(logMessage.function ?? "nil")] #\(logMessage.line): \(logMessage.message)"

        return formattedLog
    }
}

class CrashlyticsCocoaLumberjackLogger: DDAbstractLogger {
    static let sharedInstance = CrashlyticsCocoaLumberjackLogger()

    let formatter: DDLogFormatter = DefaultCrashlyticsLogFormatter()

    override func log(message logMessage: DDLogMessage) {
        let formattedMessage = formatter.format(message: logMessage)
        CLSLogv("%@", getVaList([formattedMessage ?? "(unknown log message)"]))
    }

}
```

So now, when you want to log errors in the app, use DDLogError() and it will be reported to Crashlytics.
