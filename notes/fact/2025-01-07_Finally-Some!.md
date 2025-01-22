---
date: 2025-01-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Finally Some!

In [[2025-01-07_A-New-Where-Operator|this topic]] we talked about using `> ALL` operator in WHERE clause. Here are another group of operators similar to `> ALL`:

![some-operators.png](../assets/imgs/some-operators.png)
The following value should be a list of values (or a subquery that returns a single column of values).

The comparison gets true if the value on the left is greater than some (at least one) of the values in the list.

Let's do a quick example:

> Show the name of products that are more expensive than at least one product in the 'Industrial' department.

Write the outer query:

```sql
SELECT name
FROM products
WHERE price > SOME ( );
--                  ^ subquery goes here, return single column `price` where department is 'Industrial'

```

Write inner query:

```sql
SELECT price
FROM products
WHERE department = 'Industrial';

--- OUTPUT ---
 price 
-------
   796
    72
   408
   360
   939
   309
     1
(7 rows)

```

Insert the inner query into the outer query:

```sql
-- outer query
SELECT name
FROM products
WHERE price > SOME (
  -- inner query
  SELECT price
  FROM products
  WHERE department = 'Industrial'
);

-- Products with a price greater than 1 will be selected, so almost all products should be selected.
-- And products from the 'Industrial' department itself will be selected as long as the price is greater than 1.

```
