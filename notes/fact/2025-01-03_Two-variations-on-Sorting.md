---
date: 2025-01-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Two variations on Sorting


## Sorting by a text field

In [[2025-01-03_Sorting-Records|this topic]], we sorted records by a number field, how about sorting by a text field?

```sql
SELECT *
FROM products
ORDER BY name;

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
  18 | Awesome Cotton Salad        | Shoes       |   211 |     16
  38 | Awesome Fresh Keyboard      | Home        |   982 |     30
  76 | Awesome Metal Pants         | Shoes       |   460 |      8
  57 | Awesome Steel Mouse         | Clothing    |   175 |      5
  90 | Awesome Steel Towels        | Baby        |   552 |     10
  62 | Ergonomic Fresh Pants       | Baby        |   638 |     30
  95 | Ergonomic Granite Shoes     | Beauty      |    48 |     12
  12 | Ergonomic Metal Pizza       | Jewelery    |   463 |     16
  13 | Ergonomic Steel Car         | Outdoors    |    53 |     20
--     ^^^^^^^^^^^^^ apparently, if we sort by a text field, the sorting is done alphabetically

```


## Sorting by multiple fields

```sql
SELECT *
FROM products
ORDER BY price;

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
  82 | Refined Rubber Tuna         | Garden      |     1 |     21  -- prices are the same in the records id 82 and 97
  97 | Intelligent Rubber Chicken  | Industrial  |     1 |     11
   3 | Rustic Plastic Bacon        | Movies      |    10 |      6  -- prices are the same in the records id 3 and 5
   5 | Fantastic Soft Fish         | Tools       |    10 |     10
  72 | Gorgeous Rubber Keyboard    | Baby        |    32 |      8
  93 | Tasty Granite Chips         | Home        |    37 |      9
  95 | Ergonomic Granite Shoes     | Beauty      |    48 |     12
  13 | Ergonomic Steel Car         | Outdoors    |    53 |     20

-- ...

```

We can find that there are some records with the same price. At this time, we can add another column for sorting, such as weight.

```sql
SELECT *
FROM products
ORDER BY price, weight;
--              ^^^^^^ second sorting column

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
  97 | Intelligent Rubber Chicken  | Industrial  |     1 |     11
  82 | Refined Rubber Tuna         | Garden      |     1 |     21  -- when the price is the same, the records are sorted by weight
   3 | Rustic Plastic Bacon        | Movies      |    10 |      6
   5 | Fantastic Soft Fish         | Tools       |    10 |     10  -- when the price is the same, the records are sorted by weight
  72 | Gorgeous Rubber Keyboard    | Baby        |    32 |      8
  93 | Tasty Granite Chips         | Home        |    37 |      9
  95 | Ergonomic Granite Shoes     | Beauty      |    48 |     12
  13 | Ergonomic Steel Car         | Outdoors    |    53 |     20
