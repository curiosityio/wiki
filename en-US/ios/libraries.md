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

## Map JSON arrays to Realm List

* Create this file for a custom transform (found [here](https://gist.github.com/Jerrot/fe233a94c5427a4ec29b) with just a couple small changes):

```
// Based on Swift 1.2, ObjectMapper 0.15, RealmSwift 0.94.1
// Author: Timo Wälisch <timo@waelisch.de>

import UIKit
import RealmSwift
import ObjectMapper

class ArrayTransform<T:RealmSwift.Object where T:Mappable> : TransformType {
    typealias Object = List<T>
    typealias JSON = Array<AnyObject>

    let mapper = Mapper<T>()

    func transformFromJSON(value: AnyObject?) -> List<T>? {
        let result = List<T>()
        if let tempArr = value as! Array<AnyObject>? { // swiftlint:disable:this force_cast
            for entry in tempArr {
                let mapper = Mapper<T>()
                let model: T = mapper.map(entry)!
                result.append(model)
            }
        }
        return result
    }

    // transformToJson was replaced with a solution by @zendobk from https://gist.github.com/zendobk/80b16eb74524a1674871
    // to avoid confusing future visitors of this gist. Thanks to @marksbren for pointing this out (see comments of this gist)
    func transformToJSON(value: Object?) -> JSON? {
        var results = [AnyObject]()
        if let value = value {
            for obj in value {
                let json = mapper.toJSON(obj)
                results.append(json)
            }
        }
        return results
    }
}
```

*  Use transform when mapping in model:

```
import Foundation
import RealmSwift
import ObjectMapper

class FooModel: Object, Mappable {

    var bars = List<BarModel>()    

    required convenience init?(_ map: Map) {
        self.init()
    }

    func mapping(map: Map) {
        bars <- (map["bars"], ArrayTransform<BarModel>())
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
