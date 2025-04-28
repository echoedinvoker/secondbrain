---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Filtering

We can filter retrieved data using the `WHERE` clause.

```sql
SELECT name, area FROM cities WHERE area > 4000;
--                            ^^^^^^^^^^^^^^^^^^
```

We can use multiple conditions in the `WHERE` clause to filter data by [[2025-04-20_Compound-check|compound check]] technique.
