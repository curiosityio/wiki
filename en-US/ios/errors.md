---
name: Errors
---

# Create custom Error class

```
import Foundation

public enum APIError: Error, LocalizedError {
    case parseErrorFromAPIException

    case apiCallFailure
    case api500ApiDown
    case api403UserNotEnoughPrivileges
    case api401UserUnauthorized
    case apiSome400error(errorMessage: String)

    public var errorDescription: String? {
        switch self {
        case .apiCallFailure:
            return NSLocalizedString("There seems to be an error. Sorry about that. Try again.", comment: "")
        case .api500ApiDown:
            return NSLocalizedString("The system is currently down. Come back later and try again.", comment: "")
        case .api403UserNotEnoughPrivileges:
            return NSLocalizedString("You do not have enough privileges to continue.", comment: "This should not happen.")
        case .api401UserUnauthorized:
            return NSLocalizedString("Unauthorized", comment: "")
        case .apiSome400error(let errorMessage):
            return NSLocalizedString(errorMessage, comment: "")

        case .parseErrorFromAPIException:
            return NSLocalizedString("Unknown error. Sorry, try again.", comment: "")
        }
    }
}
```

To throw error, all you need to do is: `throw APIError.apiCallFailure` or if you need to pass a param: `throw APIError.apiSome400error("foo message")`

To get the message defined for each error, do: `error.localizedDescription` to get it. 
