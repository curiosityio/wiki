---
name: Realm
---

# Delete database

```
try {
    Realm realm = Realm.getDefaultInstance();
    RealmConfiguration configuration = realm.getConfiguration();
    realm.close();

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

# Close Realm instance

Realm implements Closeable in order to take care of native memory deallocation and file descriptors so it is important to remember to close your Realm instances when you are done with them.

Realm instances are reference counted, which means that if you call getInstance() twice in a thread, you will also have to call close() twice as well.

This simply means, every time that you call Realm.getDefaultInstance(), you *must* call realm.close();

```
Realm realm = Realm.getDefaultInstance();

// do stuff with realm object

realm.close();
```

In Activities, open the realm instance in `onCreate()` and close it in `onDestroy()`.
In Fragments, open the realm instance in `onCreate()` and close it in `onDestroyView()`.

# Tips

* Because of the way that Realm does threading and handling realm instances, here are some tips for how to work with realms.
  * Query the realm via DAO object in fragments/activities. DAO objects require you to .close() the realm instance. When you .close() it, all of your queried realm models get destroyed and error if you try to access it after you .close(). Because of this, call .close() on the DAO object (which in effect closes the realm instance) in `onDestroy()` in the fragment/activity.
  * Updates, deletion, creation of realm objects (calling functions directly on a realm model instance that was queried) work with these via Kotlin extensions for a nice convenient way of interacting with them. In effect, all that these extension calls do is open a realm instance, do the operation, close the realm instance.
  * Really, what these tips are saying is that with realm, when you obtain a realm instance via `Realm.getDefaultInstance()` you need to close it and handle it correctly. You cannot use that same realm instance in different threads. All Realm data is shared between all threads, but you need to re-query when you want to access the data in different threads.
* After you edit a managed object, make sure to call `realm.copyToRealmOrUpdate()` on it in order to make sure the data is saved. [GitHub issue](https://github.com/realm/realm-java/issues/4045)
* When you want to perform a realm write, do it inside of a `realm.executeTransaction()` block instead of using `realm.beginTransaction()` and `realm.commitTransaction()`. [GitHub issue](https://github.com/realm/realm-java/issues/3975)

# Issues I have found

* After you edit a managed object, make sure to call `realm.copyToRealmOrUpdate()` on it in order to make sure the data is saved. [GitHub issue](https://github.com/realm/realm-java/issues/4045)

* When you want to perform a realm write, do it inside of a `realm.executeTransaction()` block instead of using `realm.beginTransaction()` and `realm.commitTransaction()`. [GitHub issue](https://github.com/realm/realm-java/issues/3975)
