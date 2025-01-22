---
date: 2025-01-06
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# More Useful: Subqueries with WHERE

We have previously learned to use subqueries [[2025-01-05_Subqueries-in-a-FROM|in the FROM clause]] and in [[2025-01-06_Subqueries-in-a-Join-Clause|in the Join Clause]], but it seems not very useful.

However, subqueries are more useful when used in the WHERE clause. We'll see a example to understand it better.

Assume we want to find the order ids whose product's price/weight ratio is greater than 5. We can use a subquery in the WHERE clause to achieve this.

Let's write the outer query first:
```sql
SELECT id
FROM orders
WHERE product_id IN ( );
--                   ^ we will insert a subquery which list all product ids whose price/weight ratio is greater than 5

```

subquery:

```sql
SELECT id  -- only one column, so the query will return a single column table (list of value)
FROM products
WHERE price / weight > 50;

--- OUTPUT ---
 id 
----
  1
  7
  8
  9
-- ...
 86
 87
 90
 98
(35 rows)
```

Let's insert the subquery into the outer query:

```sql
SELECT id
FROM orders
WHERE product_id IN (
  --             ^^ operator determins what shape of the subquery should be
  --                if we use =, the subquery should return a single value (single column and single row)
  --                if we use IN, the subquery should return a list of values (single column)

  -- insert the subquery here
  SELECT id
  FROM products
  WHERE price / weight > 50
);

--- OUTPUT ---
 id  
-----
  13
  15
  18
  19
  25
-- ...
 539
 542
 546
 548
 550
(201 rows)

```

This requirement can also be achieved using JOIN, but using a subquery would be easier to understand. In fact, PostgreSQL will rewrite our query, so regardless of which method we use, the performance will be the same.

