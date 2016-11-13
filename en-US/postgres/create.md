---
name: CREATE
---

# Create table

```
CREATE TABLE foo(
	id SERIAL PRIMARY KEY,
	bar_id INT REFERENCES bar(id) NOT NULL,
	created TIMESTAMP NOT NULL,
	accepted BOOLEAN DEFAULT false,
    access_token CHARACTER VARYING(256),
);
CREATE INDEX foo_indexes ON foo(bar_id);
```

This creates a table with various different types of data types. Also creates an index for the `bar_id` field that we can assume is a field that is queried often making the reason to create an index worth it. 
