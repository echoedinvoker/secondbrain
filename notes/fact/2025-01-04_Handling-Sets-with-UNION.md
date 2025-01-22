---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Handling Sets with UNION

We want to make a query that

> Find the 4 products with the highest price and the 4 products with the highest price/weight ratio.

There are two different `ORDER BY` standards for the above requirements, one is `price` and the other is `price/weight` ratio, so with our current knowledge, we can only use two queries to fulfill this requirement.

```sql
SELECT * 
FROM products
ORDER BY price DESC
LIMIT 4;

--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
 80 | Small Fresh Gloves       | Garden     |   991 |      8
  7 | Incredible Granite Mouse | Home       |   989 |      2
 38 | Awesome Fresh Keyboard   | Home       |   982 |     30
 46 | Incredible Granite Bacon | Music      |   982 |      9
(4 rows)

```

```sql
SELECT * 
FROM products
ORDER BY price / weight DESC
LIMIT 4;

--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
  7 | Incredible Granite Mouse | Home       |   989 |      2
 86 | Refined Concrete Pants   | Sports     |   724 |      2
 24 | Small Plastic Soap       | Beauty     |   345 |      1
  1 | Practical Fresh Shirt    | Toys       |   876 |      3
(4 rows)

```

Because the column of the above two query results is the same, we can use `UNION` to merge the results of these two queries.

```sql
(  -- use parentheses to wrap the query
  SELECT * 
  FROM products
  ORDER BY price DESC
  LIMIT 4  -- remove the semicolon
)
UNION  -- combine results of two queries
(
  SELECT * 
  FROM products
  ORDER BY price / weight DESC
  LIMIT 4  -- remove the semicolon
); -- add a semicolon at the end of the query


--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
 38 | Awesome Fresh Keyboard   | Home       |   982 |     30
 86 | Refined Concrete Pants   | Sports     |   724 |      2
 46 | Incredible Granite Bacon | Music      |   982 |      9
 80 | Small Fresh Gloves       | Garden     |   991 |      8
 24 | Small Plastic Soap       | Beauty     |   345 |      1
  7 | Incredible Granite Mouse | Home       |   989 |      2
  1 | Practical Fresh Shirt    | Toys       |   876 |      3
(7 rows)

```

You can find that `UNION` only gets 7 records, this is because `UNION` automatically removes duplicate data. If you want to keep duplicate data, you can use `UNION ALL`.

```sql
(
  SELECT * 
  FROM products
  ORDER BY price DESC
  LIMIT 4
)
UNION ALL
--    ^^^ add ALL to keep duplicate data
(
  SELECT * 
  FROM products
  ORDER BY price / weight DESC
  LIMIT 4
);


--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
 80 | Small Fresh Gloves       | Garden     |   991 |      8
  7 | Incredible Granite Mouse | Home       |   989 |      2  -- duplicate data
 38 | Awesome Fresh Keyboard   | Home       |   982 |     30
 46 | Incredible Granite Bacon | Music      |   982 |      9
  7 | Incredible Granite Mouse | Home       |   989 |      2  -- duplicate data
 86 | Refined Concrete Pants   | Sports     |   724 |      2
 24 | Small Plastic Soap       | Beauty     |   345 |      1
  1 | Practical Fresh Shirt    | Toys       |   876 |      3
(8 rows)

```

There are some notes about `UNION`, check [[2025-01-04_Some-notes-about-UNION|this topic]] for more details.

