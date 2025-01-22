---
date: 2025-01-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A New Where Operator

Let's do a query that:

> Show the name, department, and price of products that are more expensive than all products in the industrial department.

## Try myself

Write outer query:

```sql
SELECT name, department, price
FROM products
WHERE price > ( );
--             ^ subquery, single value

```

Write subquery:

```sql
SELECT MAX(price)
FROM products
WHERE department = 'Industrial';

--- OUTPUT ---
 max 
-----
 939
(1 row)

```

Insert subquery into outer query:

```sql
SELECT name, department, price
FROM products
WHERE price > (
  SELECT MAX(price)
  FROM products
  WHERE department = 'Industrial'
);

--- OUTPUT ---
           name            | department | price 
---------------------------+------------+-------
 Incredible Granite Mouse  | Home       |   989
 Handcrafted Rubber Towels | Baby       |   945
 Practical Rubber Mouse    | Garden     |   948
 Handmade Rubber Chicken   | Movies     |   959
 Awesome Fresh Keyboard    | Home       |   982
 Incredible Granite Bacon  | Music      |   982
 Fantastic Fresh Chips     | Home       |   966
 Small Fresh Gloves        | Garden     |   991
 Practical Steel Shoes     | Toys       |   947
(9 rows)

```

## Another way: using `> ALL` operator

![greater-than-all.png](../assets/imgs/greater-than-all.png)

The main difference from the usual comparison operator is that the value after `> ALL` needs to be a list (single column table) rather than a scalar value.

```sql
SELECT name, department, price
FROM products
WHERE price > ALL (
--          ^^^^^ replace `>` with `> ALL`
  SELECT price
  --     ^^^^^ replace `MAX(price)` with `price`, the the result of the subquery is a list of prices (single column table)
  FROM products
  WHERE department = 'Industrial'
);

--- OUTPUT --- should be the same as the previous query
           name            | department | price 
---------------------------+------------+-------
 Incredible Granite Mouse  | Home       |   989
 Handcrafted Rubber Towels | Baby       |   945
 Practical Rubber Mouse    | Garden     |   948
 Handmade Rubber Chicken   | Movies     |   959
 Awesome Fresh Keyboard    | Home       |   982
 Incredible Granite Bacon  | Music      |   982
 Fantastic Fresh Chips     | Home       |   966
 Small Fresh Gloves        | Garden     |   991
 Practical Steel Shoes     | Toys       |   947
(9 rows)

```

These two query methods have the same effect, choose which one to use based on personal preference.



