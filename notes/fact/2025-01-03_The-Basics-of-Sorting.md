---
date: 2025-01-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# The Basics of Sorting

```sql
SELECT *
FROM products
ORDER BY price;

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
  82 | Refined Rubber Tuna         | Garden      |     1 |     21
  97 | Intelligent Rubber Chicken  | Industrial  |     1 |     11
   3 | Rustic Plastic Bacon        | Movies      |    10 |      6
   5 | Fantastic Soft Fish         | Tools       |    10 |     10
  72 | Gorgeous Rubber Keyboard    | Baby        |    32 |      8
  93 | Tasty Granite Chips         | Home        |    37 |      9
  95 | Ergonomic Granite Shoes     | Beauty      |    48 |     12
  13 | Ergonomic Steel Car         | Outdoors    |    53 |     20
  74 | Small Metal Mouse           | Baby        |    60 |      6
--                                                    ^^ value of `price` is ascending (default)

-- ...

```

```sql
SELECT *
FROM products
ORDER BY price DESC;
--             ^^^^

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
  80 | Small Fresh Gloves          | Garden      |   991 |      8
   7 | Incredible Granite Mouse    | Home        |   989 |      2
  46 | Incredible Granite Bacon    | Music       |   982 |      9
  38 | Awesome Fresh Keyboard      | Home        |   982 |     30
  75 | Fantastic Fresh Chips       | Home        |   966 |     14
  37 | Handmade Rubber Chicken     | Movies      |   959 |     22
  32 | Practical Rubber Mouse      | Garden      |   948 |     15
  98 | Practical Steel Shoes       | Toys        |   947 |     14
--                                                   ^^^ value of `price` is descending

-- ...

```
```
