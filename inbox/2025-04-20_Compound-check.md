---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Compound check

`Compound check` means using multiple conditions to filter data. We can use `AND` and `OR` operators to combine more then one condition in a `WHERE` clause.

```sql
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai') AND area > 4000;
--                                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^ ^^^^^^^^^^^ 
--                                  there are two conditions combined using AND logic
```
```sql
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai') OR name = 'Delhi';
--                                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^ ^^^^^^^^^^^^^^
--                                  there are two conditions combined using OR logic
```

