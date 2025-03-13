---
date: 2025-02-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Recursive CTEs

In [[2025-02-14_Simple-Common-Table-Expressions|the last section]], we learned about simple common table expressions (CTEs). This section will focus on recursive CTEs, which is very different from simple CTEs. In my opinion, they shouldn't even share the same name.

Recursive CTEs are usefull anytime you have a tree or graph-type data structure, if not, just don't sweat it.

Recursive CTEs must use a `UNION` clause but simple CTEs don't have to.

Recursive CTEs are one of the most difficult features to understand in PostgreSQL, so I don't expect you to be able to write Recursive CTEs on your own, but I do expect that in certain scenarios you will know that you can use it to solve problems.


## Example

```sql
WITH RECURSIVE countdown(val) AS (
--   ^^^^^^^^^           ^^^ val means value, we don't use `value` because it's a reserved keyword
--   keyword for recursive CTEs
  SELECT 10
  UNION ALL
--^^^^^ recursive CTEs must use UNION
  SELECT val - 1 FROM countdown WHERE val > 1
)

SELECT * FROM countdown;


-- OUTPUT --
 val 
-----
  10
   9
   8
   7
   6
   5
   4
   3
   2
   1
(10 rows)
```
