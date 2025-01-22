---
date: 2025-01-08
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# The Case Keyword

Let's say we want to do a query that:

> Prints the name, price, and a price description for all products
> When the price is greater then 600, the price description should be "Expensive"
> When the price is greater then 300 and less then 600, the price description should be "Moderate"
> Otherwise, the price description should be "Cheap"

We can use the keyword `CASE` in `SELECT` clause to achieve this:

```sql
SELECT
  name,
  price,
  CASE
    -- wirte your case logic between `CASE` and `END`
  END AS price_desc
FROM
  products;

```

We can use the `WHEN`, `THEN`, and `ELSE` keywords to define the logic for each case:

```sql
SELECT
  name,
  price,
  CASE
    -- insert your case logic here
    WHEN price > 600 THEN 'expensive'
    WHEN price > 300 THEN 'moderate'
    ELSE 'cheap'
  END AS price_desc
FROM
  products;

--- OUTPUT ---
            name             | price | price_desc 
-----------------------------+-------+------------
 Practical Fresh Shirt       |   876 | expensive
 Gorgeous Steel Towels       |   412 | moderate
 Rustic Plastic Bacon        |    10 | cheap
 Tasty Wooden Ball           |   796 | expensive
 Fantastic Soft Fish         |    10 | cheap

-- ...

 Handcrafted Rubber Shoes    |   275 | cheap
 Intelligent Cotton Gloves   |   447 | moderate
(100 rows)

```

`ELSE` is for the default case when none of the `WHEN` conditions are met. But it's optional. If you don't provide `ELSE`, the default value will be `NULL`.

```sql
SELECT
  name,
  price,
  CASE
    WHEN price > 600 THEN 'expensive'
    WHEN price > 300 THEN 'moderate'
    -- ELSE 'cheap'  -- optional, remove it for testing
  END AS price_desc
FROM
  products;


--- OUTPUT ---
            name             | price | price_desc 
-----------------------------+-------+------------
 Practical Fresh Shirt       |   876 | expensive
 Gorgeous Steel Towels       |   412 | moderate
 Rustic Plastic Bacon        |    10 |            -- NULL
 Tasty Wooden Ball           |   796 | expensive
 Fantastic Soft Fish         |    10 | 

-- ...

 Handcrafted Rubber Shoes    |   275 | 
 Intelligent Cotton Gloves   |   447 | moderate
(100 rows)

```

We should not often see `CASE`, because this kind of logic should be handled at the application layer rather than the database layer. However, when we need to do some simple logic in SQL, `CASE` is a good choice.


