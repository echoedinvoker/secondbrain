---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Commonalities with Intersect

In [[2025-01-04_Handling-Sets-with-UNION|this topic]], we use the keyword `ALL` after `UNION` to determine whether duplicate rows in both query results should be retained.

If the requirement becomes that we only need to find the rows that are duplicated in both query results, how do we do it?

The answer is to use another set operator `INTERSECT`.

```sql
(
  SELECT * 
  FROM products
  ORDER BY price DESC
  LIMIT 4
)
INTERSECT  -- instead of UNION, use INTERSECT to find the common rows in both query results
(
  SELECT * 
  FROM products
  ORDER BY price / weight DESC
  LIMIT 4
);


--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
  7 | Incredible Granite Mouse | Home       |   989 |      2  -- the common row in both query results
(1 row)
```

`INTERSECT` can also be followed by the `ALL` keyword, just like `UNION`, but it does not to preserve duplicate rows. Instead, it **retains duplicate rows within a single query result.**


