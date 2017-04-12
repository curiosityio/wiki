---
name: Swift
---

# See if array contains item

```
class FooModel {
    var id: Int!

    init(id: Int) {
        self.id = id
    }
}

let fooArray: [FooModel] = [FooModel(1), FooModel(2)]

let doesFooArrayContainThis = FooModel(3)

let contains: Bool = fooArray.contains({$0.id == doesFooArrayContainThis.id})
```

# Create clojure

```
func doSomething() {
    let doSomethingWithData = { (data: FooModel) -> Void in
        // do something in here. It's a function. use data if you want.
    }

    doSomethingWithData(FooModel(1))    
}
```

# Multiple type generic

```
func someFunc<T where T:SomeProtocol, T:SomeOtherProtocol>(arg: T) {
    // stuff
}
```

# Multiple let statements in one line

```
var foo: FooModel?
var bar: BarModel?

if let foo = foo, bar = bar {
    // do something if foo AND bar are not null.
}
```

# Sort array of string by ABC order

```
["apple", "Banana"]?.sort(by: {$0.uppercased() < $1.uppercased()})
```

The reason we call `.uppercased()` is because the `<` operator will make `B` come before `a`. Uppercase everything (only for the sort. It does not modify your array), then compare.

# Create custom Error class

Swift has the protocol `Error` that you must inherit to create a custom error. Swift uses protocols so you can group together sets of errors together. In this example, we have a group of common HTTP errors that could happen with an API call.

Now, it is common in programming languages to associate a user friendly message with an error to display to the user for feedback. Inherit `LocalizedError` in order to provide this to your user. When an error is captured by a user, they can call `errorObject.localizedDescription` to get the user friendly string from the user.

The use of the comment I am not completely sure about. From what I have gotten from research, it's a comment for the developer. This is where I am using it to say "This should never happen" for one of the errors. It tells me it's a programming error instead of user human error.

Below I have examples of custom errors that provide an error message to you and others that you can provide an error to you.

```
import Foundation

enum APIError: Error, LocalizedError {
    case api500systemError
    case api403forbidden
    case api401unauthorized
    case apiSome400error(errorMessage: String)

    public var errorDescription: String? {
        switch self {
        case .api500systemError:
            return NSLocalizedString("The system is currently down. Come back later and try again.", comment: "")
        case .api403forbidden:
            return NSLocalizedString("You do not have enough privileges to continue.", comment: "This should not happen.")
        case .api401unauthorized:
            return NSLocalizedString("Sorry, you must login again.", comment: "")
        case .apiSome400error(let errorMessage):
            return NSLocalizedString(errorMessage, comment: "User error.")
        }
    }
}
```

Now, with these errors defined, we can throw errors:

```
throw APIError.api500systemError // Error already ready for us with localizedDescription populated for us. That is all we need to do.
throw APIError.apiSome400error("The user did something they should not have done. Tell them what they did wrong here.") // Here, you provide the error message and it will be provided to the user by populating the localizedDescription. 
```
