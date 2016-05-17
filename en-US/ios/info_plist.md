---
name: Info.plist
---

# Get value for key in Info.plist file

```
import Foundation

class ClientCredsUtil {

    static var clientId: String?

    static let clientIdKey: String = "Key in Info plist file goes here"

    class func getClientId() -> String {
        if (clientId == nil) {
            clientId = InfoPlistUtil.getValueFromKey(clientIdKey) as? String
            if (clientId == nil) {
                fatalError(String(format: "Add '@%' to Info.plist with key string value.", clientIdKey))
            }
        }

        return clientId!
    }

}
```

where `InfoPlistUtil`:

```
import Foundation

class InfoPlistUtil {

    class func getValueFromKey(key: String) -> AnyObject? {
        let mainBundle = NSBundle.mainBundle()

        return mainBundle.objectForInfoDictionaryKey(key)
    }

}
```
