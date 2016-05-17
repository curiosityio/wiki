---
name: Core Data
---

# Delete entity from core data

An entity is like a table in databases.

```
class func deleteAllCoreData() {
    deleteEntity("User")
    deleteEntity("Foo")

    saveCoreData()
}

private class func deleteEntity(entityName: String) {
    let userFetchRequest = NSFetchRequest(entityName: entityName)
    do {
        let fetchedUserObjects = try getManagedContext().executeFetchRequest(userFetchRequest) as! [NSManagedObject]
        for (object) in fetchedUserObjects {
            self.getManagedContext().deleteObject(object)
        }
    } catch {
        fatalError("Error fetching and deleting data.")
    }
}

private class func saveCoreData() {
    do {
        try getManagedContext().save()
    } catch {
        fatalError("Error saving core data")
    }
}

class func getManagedContext() -> NSManagedObjectContext {
    return getAppDelegate().managedObjectContext
}

private class func getAppDelegate() -> AppDelegate {
    return UIApplication.sharedApplication().delegate as! AppDelegate
}
```
