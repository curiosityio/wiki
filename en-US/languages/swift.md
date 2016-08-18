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
