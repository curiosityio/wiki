---
name: SQLite
---

# Replace text for column in database.

```
update tableName set  
  columnName = replace(columnName, '/content/images/', '/blog/content/images/')
where  
  columnName like '/content/images/%';
```

Example:

If a table, named `posts`, have a column named `image` that currently has the text `/content/images/2015/02/levi.jpg` you want to prepend `/blog` to this text to create `/blog/content/images/2015/02/levi.jpg`, this would be the SQL to do it.

```
update posts set  
  image = replace(image, '/content/images/', '/blog/content/images/')
where  
  image like '/content/images/%';
```
