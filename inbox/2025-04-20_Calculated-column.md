---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Calculated column

We can calculate a new column from the original columns in the table.

Example in SELECT clause:
```sql
SELECT name, population / area FROM cities;
--           ^^^^^^^^^^^^^^^^^ calculated column
```
Example in WHERE clause:
```sql
SELECT name FROM cities WHERE population / area > 8000;
--                            ^^^^^^^^^^^^^^^^^ calculated column
```
It's common to [[2025-04-20_Alias|set an alias]] for the calculated column

