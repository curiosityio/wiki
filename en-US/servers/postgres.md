---
name: Postgres
---

# Postgres

Postgres is a solid relational database. My go-to for working on projects.

Here is some documentation for setting postgres up for the first time:

* [Install](#install)
* [Configure](#configure)

If you have postgres installed already, here is some info on setting postgres up for create applications with:

* [Create user role for application](#create-user-role-for-application)
* [Create database for application](#create-database-for-application)

Here are some useful bash commands:

* `psql -U user_name` to login to postgres as a specific postgres role.
* `psql -U user_name -d database_name -h 127.0.0.1 -W`  to login to postgres as a specific postgres role, and to work on a specific database.

Here are some useful postgres commands once inside of postgres:

* `\q` quit postgres.
* `\du` list all postgres users (aka: roles).

---

# Install

Install postgres via apt-get:

```
sudo apt-get update; sudo apt-get install -y postgresql postgresql-contrib;
```
This is not all however. Move on to [configure](#configure) to finish setup.

# Configure

##### Create root postgres user to access psql as our logged in user.

Postgres works by associating a Linux user account to postgres user accounts (a linux user account "levi" must have a postgres account created name "levi" or you cannot use postgres). By default, a linux user account, named "postgres", is created when you install postgres. This user can do anything and everything to postgres and all of it's databases.

Lets create a postgres account, named "root", so we can login to the droplet with our Linux user account "root" and be able to use postgres.
```
sudo -i -u postgres
```
...to login to linux account postgres.
```
createuser --interactive
```
...to create a new postgres account (type: root as username as that is the linux account we want to have work and "y" when asked about superuser so we can do whatever.)

This command only creates a postgres user. To finish, we need to create a postgres database with the same name as our postgres user (postgres simply requires having a database for each user account). So lets do that now:
```
createdb root
```
...will create the database for our root linux and postgres user account.

Done. Now you can `exit` to sign out of the "postgres" linux account and go back to our "root" linux user. Now, `psql` under the root account and you should be in postgres. Success!

# Create user role for application

For our application, we want to create a new postgres user account with lower privileges to run on our database. A user account that cannot create new databases, only has access to read/write to one DB for the application, does not have super user access, etc.

First go to postgres: `psql`. Then run this command inside of psql to create our new user (replace 'user_name' with the name of our application):
```
create role user_name with login;
```
...to create the user (aka: role as postgres likes to call it). Now we need to give this new user a password: (note: passwords cannot have ! character in them. Also, replace 'user_name' again with the username used in the above command when creating a user):
```
\password user_name
```
...after following postgres prompts to create a password, we need to create a postgres database to match the name of the user:
```
createdb user_name
```
It is good to create this database now as the root or postgres linux user so that they are the ones who have access to modify it (whatever linux/postgres user creates a database is the owner of it)

The user is now created.

Done. Previously we talked about how postgres users line up with linux system users. Well here, we didn't create a linux user only a postgres "role". We will only use this new "role" in our application code to login to the database and work with it.

Now lets create a database for this new user.

# Create database for application

These directions are assuming you have created a postgres user (aka: role) made just for your application. If not, check out: [Create user role for application](#create-user-role-for-application).

As our "postgres" or "root" linux user, lets create the postgres database for the application (replace "application_name" with your application name. I like to tag on `_application` to the end of the database name because I tend to create my application username the name of the application and because postgres requires a database be created for each postgres user, I must have a different database name as my user account).
```
createdb application_name_application
```

Now go into postgres, `psql`, and grant privileges to your user_name to this new application database (if you forget what postgres users there are, go into postgres `psql` and use command `\du` to list them all).
```
grant all privileges on database database_name to use_name;
```

Done. You can login as this user anytime with: `psql -U user_name -d database_name -h 127.0.0.1 -W`

# Create database connection URL

```
export DATABASE_URL="postgres://postgres_username:postgres_password@localhost:5432/postgres_db_name"
```

# Run sql file queries against postgres

```
psql -U postgres_username -d databasename -f filename.sql -h 127.0.0.1
```
