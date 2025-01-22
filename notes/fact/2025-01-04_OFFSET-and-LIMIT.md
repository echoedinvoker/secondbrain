---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# OFFSET and LIMIT

![concept-offset-limit.png](../assets/imgs/concept-offset-limit.png)

Some common usage of `OFFSET` and `LIMIT` in SQL queries:

**Pick the most expensive 3 products**

`LIMIT` often used in conjunction with `ORDER BY` to get the top or bottom N rows.

```sql
SELECT *
FROM products
ORDER BY price DESC
LIMIT 5;

--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
 80 | Small Fresh Gloves       | Garden     |   991 |      8
  7 | Incredible Granite Mouse | Home       |   989 |      2
 46 | Incredible Granite Bacon | Music      |   982 |      9
 38 | Awesome Fresh Keyboard   | Home       |   982 |     30
 75 | Fantastic Fresh Chips    | Home       |   966 |     14
(5 rows)
```

Getting the top one most row is a common use case for `LIMIT` as well.

```sql
SELECT *
FROM products
ORDER BY price DESC
LIMIT 1;

--- OUTPUT ---
 id |        name        | department | price | weight 
----+--------------------+------------+-------+--------
 80 | Small Fresh Gloves | Garden     |   991 |      8
(1 row)
```

As for `OFFSET`, it is often used together with `LIMIT` to retrieve data within a certain range, with the most common example being used for pagination.

```sql
SELECT *
FROM products
LIMIT 10  -- assuming 10 rows per page
OFFSET 0;  -- page 1

--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
  1 | Practical Fresh Shirt    | Toys       |   876 |      3
  2 | Gorgeous Steel Towels    | Outdoors   |   412 |     16
  3 | Rustic Plastic Bacon     | Movies     |    10 |      6
  4 | Tasty Wooden Ball        | Industrial |   796 |     23
  5 | Fantastic Soft Fish      | Tools      |    10 |     10
  6 | Gorgeous Concrete Towels | Grocery    |   328 |     11
  7 | Incredible Granite Mouse | Home       |   989 |      2
  8 | Gorgeous Rubber Ball     | Books      |   801 |      4
  9 | Generic Fresh Computer   | Toys       |   926 |     11
 10 | Unbranded Cotton Shoes   | Sports     |   298 |     29
(10 rows)
```

```sql
SELECT *
FROM products
LIMIT 10
OFFSET 10;  -- page 2

--- OUTPUT ---
 id |            name             | department | price | weight 
----+-----------------------------+------------+-------+--------
 11 | Fantastic Metal Chair       | Home       |   887 |      9
 12 | Ergonomic Metal Pizza       | Jewelery   |   463 |     16
 13 | Ergonomic Steel Car         | Outdoors   |    53 |     20
 14 | Licensed Steel Car          | Movies     |   664 |     10
 15 | Tasty Metal Cheese          | Beauty     |   650 |     17
 16 | Handcrafted Rubber Towels   | Baby       |   945 |      6
 17 | Intelligent Metal Mouse     | Music      |   509 |      7
 18 | Awesome Cotton Salad        | Shoes      |   211 |     16
 19 | Unbranded Plastic Pizza     | Industrial |    72 |      9
 20 | Practical Concrete Sausages | Industrial |   408 |      9
(10 rows)

```

The order of `LIMIT` and `OFFSET` does not matter, but usually `LIMIT` comes first.

`OFFSET` does not necessarily have to be used together with `LIMIT`, it can also be used alone.

