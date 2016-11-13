---
name: Postgres triggers.
---

Triggers are functions that are executed by Postgres. They are functions you write that can update database data when you specify. *Not* a query. A function of code.

# When do I use triggers?

Lets say that you have a social media application. You give your users the ability to follow others like on Twitter.

The user table in my postgres database might look like this:

```
id  | first name | last name  | followers | following
-----------------------------------------------------
24  | Levi       | Bostian    | 23        | 14
25  | Joe        | Smith      | 45        | 24
```

Not a very popular social media app, haha!

Then, you have a table that keeps track of who follows who:

```
id  | follower_id | following_id | active |
-------------------------------------------
2   | 24          | 25           | true
```

From this table row 2, you can see that Levi is following Joe. `active` is like saying it's deleted. Rather then deleting row from database table, I can mark this as false and it's no longer active.

Now, whenever a user wants to know how many followers that they have, I *can* query the database each and every time through the "following" table above to see how many `following_id` has the ID of 24 to count how many followers Levi has. That seems expensive to me and not needed.

So, I create a Postgres trigger function. Every time that a row is added to "following" table, I update the "followers" and "following" columns for the users table. Then, I can simply return this one row from the database when users want to know how many followers and folling they have.

```
CREATE OR REPLACE FUNCTION increment_followers_following_function() RETURNS TRIGGER AS $increment_followers_following_function$
       BEGIN
           UPDATE users SET followers=(followers + 1) WHERE id=NEW.following_id;
           UPDATE users SET following=(following + 1) WHERE id=NEW.follower_id;
           RETURN NEW;
       END;
$update_followers_following_function$ LANGUAGE plpgsql;
CREATE TRIGGER increment_followers_following_trigger AFTER INSERT ON following FOR EACH ROW EXECUTE PROCEDURE increment_followers_following_function();
```

This code above is our SQL that we would execute on our postgres database to create the function and then create the trigger to run after insert on following table to run the function.

You can also create another function for decrementing after someone unfollows:

```
CREATE OR REPLACE FUNCTION decrement_followers_following_function() RETURNS TRIGGER AS $decrement_followers_following_function$
       BEGIN
       IF NOT NEW.active THEN
           UPDATE users SET followers=(followers - 1) WHERE id=NEW.following_id;
           UPDATE users SET following=(following - 1) WHERE id=NEW.follower_id;
       ELSE
           UPDATE users SET followers=(followers + 1) WHERE id=NEW.following_id;
           UPDATE users SET following=(following + 1) WHERE id=NEW.follower_id;
       END IF;
       RETURN NEW;
       END;
$update_followers_following_function$ LANGUAGE plpgsql;
CREATE TRIGGER decrement_followers_following_trigger AFTER UPDATE ON following FOR EACH ROW EXECUTE PROCEDURE decrement_followers_following_function();
```

Here, I changed the name of the function and trigger from `increment_...` to `decrement_...`, I changed the `+` to `-` to subtract in the users table, and changed `AFTER INSERT` to `AFTER UPDATE` when the trigger function would run.

See I even have a conditional statement in here as well. Depending on if the status of active changes from true/false depends on what happens.

You will have to have a little logic in your API code to make this code work successfully. You don't want someone to send up a bunch of API calls for unfollowing a user that you are already unfollowing. The trigger function runs on every update therefore, you don't want it to run when there is no update needed. If someone calls the API saying to unfollow someone and you are already unfollowing them, skip the call and don't execute an update on the database. Or else, someone could screw up these figures way off! (one con to using triggers). 
