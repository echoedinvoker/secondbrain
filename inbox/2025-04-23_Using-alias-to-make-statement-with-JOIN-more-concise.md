---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Using alias to make statement with JOIN more concise

It's common to use aliases in SQL queries to make them more concise and readable especially when dealing with multiple tables and complex joins.

```sql
SELECT
  c.id
FROM
  comments AS c
JOIN
  photos AS p ON p.id = c.photo_id;
```

In this example, `comments` is aliased as `c` and `photos` is aliased as `p`. This allows us to refer to the tables using shorter names, making the query easier to read and write.

> Note that AS is actually a rename, so if the part using alias in the example above is changed to original name, an error will occur.
