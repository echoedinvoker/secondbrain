---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# If column names collide between tables

We need to add table prefixes to the column names in the `SELECT` statement to avoid ambiguity.

```sql
SELECT
  id  -- this is ambiguous because both tables have an id column,
      -- so we need to specify which one we want such as photos.id
      -- or the error will be thrown
FROM
  comments
JOIN
  photos ON photos.id = comments.photo_id;
```
