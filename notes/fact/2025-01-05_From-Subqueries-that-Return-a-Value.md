---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# From Subqueries that Return a Value

In [[2025-01-05_Subqueries-in-a-FROM|this topic]], we learned that a subquery inserted to FROM clause can be any shape of data, let's try to use a subquery that returns a value.

subquery:

```sql
SELECT MAX(price)
FROM products;

--- OUTPUT ---
 max 
-----
 991
(1 row)

```

Insert it to the FROM clause of a query:

```sql
SELECT *
FROM (
  SELECT MAX(price)
  FROM products
) as p;


--- OUTPUT --- works fine :D
 max 
-----
 991
(1 row)

```

It works fine, and also following the same rules of the subquery in the FROM clause.

Let's try to break the rules:

```sql
SELECT price
--     ^^^^^ the column does not exist in the subquery
FROM (
  SELECT MAX(price)
  --         ^^^^^ this is easy to misleading newbie ._.
  FROM products
) as p;


--- OUTPUT --- ERROR, because the column `price` does not exist in the subquery, we can only use column `max` or `*` in this case.
psql:/tmp/nvim.mattc/kWvttb/big_data-query-2025-01-05-14-55-10:5: ERROR:  column "price" does not exist
LINE 1: SELECT price
               ^
```

```sql
SELECT max
FROM (
  SELECT MAX(price)
  FROM products
) -- remove the alias

--- OUTPUT --- ERROR, because we forget to add an alias to the subquery even it only returns a value.
psql:/tmp/nvim.mattc/kWvttb/big_data-query-2025-01-05-14-55-10:5: ERROR:  subquery in FROM must have an alias
LINE 2: FROM (
             ^
HINT:  For example, FROM (SELECT ...) [AS] foo.
```

