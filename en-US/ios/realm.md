---
name: iOS Realm
---

# Multiple Realm instances

Realm instances can be thought of as databases. Your app most of the time will need to use just one database. However, sometimes you need to have multiple instances of Realm. If you have an app that has user accounts, I recommend having a separate Realm instance for each user so when they log out, you do not need to delete the database data for them.

This is how you would usually obtain a Realm instance

```
let realm = try! Realm()
```

This reads/writes data to/from the "default.realm" file. One database. This is how you can create multiple instances:

```
let documentDirectory = try! FileManager.default.url(for: .documentDirectory, in: .userDomainMask,
                                                        appropriateFor: nil, create: false)
let url = documentDirectory.appendingPathComponent("foo.realm")
var config = Realm.Configuration()
config.fileURL = url
let fooRealm = try! Realm(configuration: config)
```
