---
name: DBFlow ORM library.
---

* [Errors](#errors)

# Errors

* **...is not registered with a Database. Did you forget the @Table annotation?**
When using DBFlow with Dagger (1 and 2) and some other libraries you get this error often but not every time. [This issue](https://github.com/Raizlabs/DBFlow/issues/685) goes over the issue and solution. Long story short, from command line (not Android Studio) run `./gradlew clean`, uninstall app on device, then run it again.

When you make changes to any of the DBFlow table/database structure you need to do a full clean in order for the changes to happen when using with some libraries like Dagger.
