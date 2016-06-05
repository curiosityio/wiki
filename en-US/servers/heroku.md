---
name: Heroku
---

# Postgres

## Add postgres to a dyno

```
heroku addons:create heroku-postgresql:hobby-dev
```

## Run .sql files against postgres dyno

```
cat file.sql | heroku pg:psql
```

## Connect via pgadmin3

Connecting to heroku postgres database remotely you need the connection settings.

* Go to heroku.com, login, then from the dashboard select your heroku project.
* From the add-ons section select postgres.
* Your connection settings will appear. 
