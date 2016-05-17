---
name: NSUserDefaults
---

# Delete all NSUserDefaults data

```
class func deleteAllUserDefaultsData() {
    if let appDomain = NSBundle.mainBundle().bundleIdentifier {
        NSUserDefaults.standardUserDefaults().removePersistentDomainForName(appDomain)
    }    
}
```

# Write to NSUserDefaults

```
static let userProfileTokenKey = "userProfileTokenKey"

class func saveUserToken(userToken: String) {
    NSUserDefaults.standardUserDefaults().setObject(userToken, forKey: userProfileTokenKey)
    forceWrite()
}

private class func forceWrite() {
    NSUserDefaults.standardUserDefaults().synchronize()
}
```
