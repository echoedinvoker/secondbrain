---
date: 2025-01-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A SELECT Without a From?
 
If there are only subqueries in the SELECT clause, that query can be written without a FROM clause.

Let's see an example:

```sql
SELECT (
  -- insert subquery directly, no any outer column
  -- remember the rule of subquery in SELECT clause: it must return exactly one column and one row
  SELECT MAX(price)
  FROM products
);  -- no FROM clause

--- OUTPUT ---
 max 
-----
 991  -- it is allowed
(1 row)

```

But, what is it for?

Answer is that you can get a single value from multiple values, let's see another example directly:
 
```sql
SELECT (
  SELECT MAX(price)
  FROM products
) / (
  SELECT AVG(price)
  FROM products
);


--- OUTPUT ---
      ?column?      
--------------------
 1.9870470996330680
(1 row)

```

This query is much more meaningful than the previous one. It calculates the ratio of the maximum price to the average price of the products.

