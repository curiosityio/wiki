---
name: pg_dump
---

```
pg_dump -h 127.0.0.1 -U username -d database-name   > /tmp/schema.sql
```

This will connect to your localhost's database (replace `127.0.0.1` with another IP address to address a remote server) and dump it's schema into a file. It will dump the entire schema as well as the data.

```
pg_dump -h 127.0.0.1 -U username --column-inserts --data-only -d database-name   > /tmp/schema.sql
```

This above will give you *only* the `INSERT` statements for your data. `--column-inserts` gives you `INSERT` statements instead of `COPY` postgres statements. `--data-only` is for getting only the data and not rest of schema. 
