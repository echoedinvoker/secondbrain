---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Whats a Subquery?

![req-subq.png](../assets/imgs/req-subq.png)

In order to fulfill the requirements on the left side above, I need to do two queries. It is obvious that the WHERE clause of the second query needs to use the results of the first query.

Instead of running two separate queries, I can use a subquery to combine the two queries into one.

The trick to using subqueries is to write the outer(second) query first:

```sql
SELECT name, price
FROM products
WHERE price > ( );
--             ^ this value comes from the inner(first) query

```

Then, write the inner(first) query inside the parentheses:

```sql
SELECT name, price
FROM products
WHERE price > (
  -- write the inner inside the parentheses
  SELECT MAX(price)
  FROM products
  WHERE department = 'Toys'
);

--- OUTPUT ---
           name           | price 
--------------------------+-------
 Incredible Granite Mouse |   989
 Practical Rubber Mouse   |   948
 Handmade Rubber Chicken  |   959
 Awesome Fresh Keyboard   |   982
 Incredible Granite Bacon |   982
 Fantastic Fresh Chips    |   966
 Small Fresh Gloves       |   991
(7 rows)

```

