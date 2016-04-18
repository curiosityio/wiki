---
name: DBFlow ORM library.
---

# How to run queries.
All examples came from [offical doc](https://github.com/Raizlabs/DBFlow/blob/master/usage/SQLQuery.md). More examples there. 

* [Count query](#count-query)
* [Select query](#select-query)
* [Update query](#update-query)
* [Errors](#errors)

# Count query

How many users have the username "levi"?
```
long count = SQLite.selectCountOf().where(UserModel_Table.username.is("levi")).count();
```

# Select query

This example gets all users with username "levi".
```
List<UserModel> users = SQLite.select().from(UserModel.class).where(UserModel_Table.username.is("levi")).queryList();
```
This run synchronously. It returns a list of results.

If you want to return just 1 result:
```
UserModel user = SQLite.select().from(UserModel.class).where(UserModel_Table.username.is("levi")).querySingle();
```

# Update query

This example updates the UserModel username for a user to "levi" where it is currently set at "sam".
```
SQLite.update(UserModel.class)
              .set(UserModel_Table.username.eq("levi"))
              .where(UserModel_Table.username.is("sam")).query();
```
This run synchronously.

# Errors

* **...is not registered with a Database. Did you forget the @Table annotation?**

When using DBFlow you get this error often but not every time. [This issue](https://github.com/Raizlabs/DBFlow/issues/685) goes over the issue and solution. Long story short, from command line (not Android Studio) run `./gradlew clean`, then build/install/run from studio again.

When you make changes to any of the DBFlow table/database/dao you need to do a full clean in order for the changes to happen.
