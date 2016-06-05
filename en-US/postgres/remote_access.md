---
name: Postgres remote access
---

If you want to have your postgres database accessible for remote connections to it for application servers or for a tool like PGAdmin3, this is how:

* Edit the file "/etc/postgresql/9.3/main/postgresql.conf" and change the "listen" line from: `#listen_addresses = 'localhost'` to `listen_addresses = '*'`
* Edit the file "/etc/postgresql/9.3/main/pg_hba.conf" and allow connection by all users to all databases from any IP if the user has right MD5-encrypted password by adding the following line at the end of the file:
```
host all all 0.0.0.0/0 md5
```
Of course change the fields above to be more restrictive if you choose to only allow access to certain databases, certain users, certain IPs.

* Restart postgres:

```
sudo service postgresql stop
sudo service postgresql start
```

* Open PGAdmin3, create a new connection and fill in the details like this:
![](/docs/images/pg_admin3_new_connection.png)
