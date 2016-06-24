---
name: iOS libraries
---

# SDWebImage

### Scrolling in tableview make images not choppy loading and setting rows incorrectly.

https://github.com/rs/SDWebImage/issues/1397#event-675492737

# ObjectMapper

## Map strings to NSDate

* Create a custom transformer
*Note: I am using the library [SwiftDate](https://github.com/malcommac/SwiftDate) for this example. It's easier to deal with NSDate*

```
import Foundation
import SwiftDate
import ObjectMapper

public class NSDateTransform: TransformType {

    public typealias Object = NSDate
    public typealias JSON = String

    public init() {}

    public func transformFromJSON(value: AnyObject?) -> NSDate? {
        if let timeString = value as? String {
            return timeString.toDate(DateFormat.ISO8601Format(.Extended))
        }

        return nil
    }

    public func transformToJSON(value: NSDate?) -> String? {
        if let date = value {
            return date.toString(DateFormat.ISO8601Format(.Extended))
        }

        return nil
    }

}
```

* In mapped object, specify this transformer:

```
import Foundation
import ObjectMapper
import SwiftDate

class FooVo: Mappable {

    dynamic var created: NSDate = NSDate()

    required convenience init?(_ map: Map) {
        self.init()
    }

    func mapping(map: Map) {
        created <- (map["created"], NSDateTransform())
    }

}
```

## Get ObjectMapper and Realm to work well together in a model object.

```
import Foundation
import RealmSwift
import ObjectMapper
import SwiftDate

class FooModel: Object, Mappable {

    dynamic var id: Int = 0
    dynamic var datehere: NSDate = NSDate()
    dynamic var fooTitle: String = ""
    dynamic var done: Bool = false
    dynamic var otherDate: NSDate? = nil
    dynamic var bar: BarModel?

    override static func primaryKey() -> String? {
        return "id"
    }

    required convenience init?(_ map: Map) {
        self.init()
    }

    func mapping(map: Map) {
        id <- map["id"]
        datehere <- (map["datehere"], NSDateTransform())
        fooTitle <- map["foo_title"]
        done <- map["done"]
        otherDate <- (map["other_date"], NSDateTransform())
        bar <- map["bar"]
    }

}
```
