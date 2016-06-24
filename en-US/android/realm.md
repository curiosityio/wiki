---
name: Realm
---

# Delete database

```
try {
    RealmConfiguration configuration = Realm.getDefaultInstance().getConfiguration();
    Realm.getDefaultInstance().close();
    Realm.deleteRealm(configuration);
} catch (Exception e) {
    // .close() can throw an exception. Catch and log it here. 
}
```

# Create realm models but dont save to storage

Lets say you have a real model named `User`:

```
public class User extends RealmObject {

    public String name;
    public int age;
    @Ignore public int sessionId;

}
```

Here is how you can create a realm object but not save it to storage (also how to save to storage later if you like):

```
User user = new User("John");
user.setEmail("john@corporation.com");
// use user object however you like and it is not saved to storage.

// if you want to save user to storage later on:
realm.beginTransaction();
User realmUser = realm.copyToRealm(user);
realm.commitTransaction();
// Note: Any further changes must happen on realmUser.
```

*Note: When using realm.copyToRealm() it is important to remember that only the returned object is managed by Realm, so any further changes to the original object will not be persisted.*

[official docs](https://realm.io/docs/java/latest/#creating-objects)
