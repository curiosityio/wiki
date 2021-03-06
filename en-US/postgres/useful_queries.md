---
name: Postgres useful queries
---

Scenarios:

* You are querying in DESC order with a limit. [docs]('#query-desc-with-limit')
* You are querying in DESC order with a limit, but want result to be in ASC order. [docs]('#query-desc-with-limit-but-want-result-in-asc-order')
* You have an existing column in your database that allows null values. You don't want null values anymore. [docs]('#make-existing-column-in-database-not-allow-null-values-anymore')
* Remove NOT NULL constraint from existing column in database. [docs](#remove-not-null-constraint-from-existing-column-in-database)

# Query DESC with limit

Example scenario.

I have this table with IDs:  
```
1  
2  
3  
4  
5  
```
I want a `select * from table where id < 5 limit 2`. I expect to receive back:  
```
4  
3  
```
However, I do not get 4, 3 back. I get:  
```
1
2
```
So lets change our query to: `select * from table where id < 5 order by id DESC  limit 2;` I get back
```
4
3
```
Like I wanted. (want the end result `4, 3` to be in ASC order? [docs]('#query-desc-with-limit-but-want-result-in-asc-order'))

##### Postgres
```
SELECT * FROM tablename WHERE user_id=2 AND id < 100 ORDER BY id DESC LIMIT 20;
```

##### SquelJS
```
var squel = require('squel').useFlavour('postgres');

squel.select()
.field('*')
.from('tablename')
.where('user_id=2')
.where('id < 100')
.order("id", false)
.limit(20)
.toString()
```

# Query DESC with limit but want result in ASC order

Example scenario.

I have this table with IDs:  
```
1  
2  
3  
4  
5  
```
I want a `select * from table where id < 5 limit 2`. I expect to receive back:  
```
4  
3  
```
However, I do not get 4, 3 back. I get:  
```
1
2
```
So lets change our query to: `select * from table where id < 5 order by id DESC limit 2;` I get back
```
4
3
```
Like I wanted. However, it is in the wrong order. I want it to instead be:
```
3
4
```
(asc order).

The way to do this is to do an inner join with a select * tablename of the same table to join the two together.

##### Postgres
```
SELECT a.* FROM
  (SELECT * FROM tablename WHERE (client_id=2) AND (id < 100) ORDER BY id DESC LIMIT 20) AS a
    INNER JOIN (SELECT id FROM tablename ORDER BY id ASC) AS b
    ON (a.id=b.id);
```

##### SquelJS
```
var squel = require('squel').useFlavour('postgres');

squel.select()
.field('a.*')
.from(
    squel.select()
    .field('*')
    .from('tablename')
    .where('client_id=2')
    .where('id < 100')
    .order("id", false)
    .limit(20),
    'a'
)
.join(
    squel.select()
    .field('id')
    .from('tablename')
    .order('id'),
    'b',
    squel.expr().and('a.id=b.id')
).toString();
```

# Make existing column in database not allow NULL values anymore

Lets you have a table:

```
create table users(
    first_name character varying(10);
);
```

One day you decide you do not want first_name to have null values anymore.

```
update users set first_name='' where first_name is null; -- required to avoid error 'first_name contains null values'
alter table users alter column first_name set default '';
alter table users alter column first_name set NOT NULL;
```

You can change the default value to something else.

# Remove NOT NULL constraint from existing column in database

Lets you have a table:

```
create table users(
    first_name character varying(10) NOT NULL;
);
```

One day you decide you do not want first_name to be able to be null.

```
ALTER TABLE users ALTER COLUMN first_name DROP NOT NULL;
```
