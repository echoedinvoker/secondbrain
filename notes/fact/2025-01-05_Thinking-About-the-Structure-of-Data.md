---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Thinking About the Structure of Data

We can insert a subquery in different parts of a query. Here is an example:

```sql
SELECT
    p1.name,
    (SELECT COUNT(name) FROM products)
--  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
FROM
    (SELECT * FROM products) as p1
--  ^^^^^^^^^^^^^^^^^^^^^^^^
JOIN
    (SELECT * FROM products) as p2 ON p1.id = p2.id
--  ^^^^^^^^^^^^^^^^^^^^^^^^
WHERE
    p1.id IN (SELECT id FROM products);
--           ^^^^^^^^^^^^^^^^^^^^^^^^^
```

When we use a subquery, we must pay attention to the **shape** of the data it returns.

```sql
SELECT COUNT(name) FROM products;

--- OUTPUT ---
 count 
-------
   100  -- only one column and one row, basically a single value
        -- when a query returns a single value, it is called a **scalar query**
(1 row)

```

```sql
SELECT * FROM products;

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
   1 | Practical Fresh Shirt       | Toys        |   876 |      3
   2 | Gorgeous Steel Towels       | Outdoors    |   412 |     16
   3 | Rustic Plastic Bacon        | Movies      |    10 |      6
   4 | Tasty Wooden Ball           | Industrial  |   796 |     23
   5 | Fantastic Soft Fish         | Tools       |    10 |     10  -- many rows and columns

-- ...

```
```sql
SELECT id FROM products;

--- OUTPUT ---
 id  
-----
   1
   2
   3
   4
   5
   6
   7  -- only one column and many rows

-- ...

```

Remember that the return values of subqueries mainly have these three shapes above, it's important to use multiple subqueries in a complex query.

