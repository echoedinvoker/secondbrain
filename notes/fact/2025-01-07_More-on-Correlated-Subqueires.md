---
date: 2025-01-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# More on Correlated Subqueires

In [[2025-01-07_Probably-Too-Much-About-Correlated-Subqueries|this topic]], we learned about correlated subqueries. Let's do more query with it to understand it better.

Let's say we want to do a query that:

> Without using a join or a group by, print the number of orders for each product.

Although the description above may instinctively lead us to use `GROUP BY` or `JOIN` to solve the problem, it explicitly tells us not to use these two methods, so we can only use subqueries to solve this problem.

Write the outer query:

```sql
SELECT name, ( )
--            ^ here should be filled with subquery which return a scalar value (number of orders for each product)
FROM products

```

subquery:

```sql
SELECT COUNT(*)
FROM orders
WHERE product_id = 
--                 ^^^^^^^ should be filled with the product id from the outer query

```

Insert the subquery to the outer query:

```sql
-- outer query
SELECT name, (
  -- inner subquery return the number of orders for each product
  SELECT COUNT(*)
  FROM orders as i
  WHERE product_id = o.id
  --                 ^^^^ product id from the outer query
)
FROM products AS o
--            ^^^^ alias for using in the inner subquery

```
