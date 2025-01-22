---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Removing Commonalities with EXCEPT

![except-diagram.png](../assets/imgs/except-diagram.png)

The above is the operation process of the `EXCEPT` set operator. You can see that it is based on the results of **the first query** for exclusion, so **the order of the queries will affect the results**, which is different from other set operators and should be noted.

```sql
(
  SELECT * 
  FROM products
  ORDER BY price DESC
  LIMIT 4
)
EXCEPT
(
  SELECT * 
  FROM products
  ORDER BY price / weight DESC
  LIMIT 4
);

--- OUTPUT ---
 id |           name           | department | price | weight 
----+--------------------------+------------+-------+--------
 38 | Awesome Fresh Keyboard   | Home       |   982 |     30
 46 | Incredible Granite Bacon | Music      |   982 |      9
 80 | Small Fresh Gloves       | Garden     |   991 |      8
(3 rows)

```
```sql
(
  SELECT * 
  FROM products
  ORDER BY price / weight DESC   -- Change the order of the queries
  LIMIT 4
)
EXCEPT
(
  SELECT * 
  FROM products
  ORDER BY price DESC
  LIMIT 4
);

--- OUTPUT ---  You can see that the order of the queries affects the results.
 id |          name          | department | price | weight 
----+------------------------+------------+-------+--------
 86 | Refined Concrete Pants | Sports     |   724 |      2
 24 | Small Plastic Soap     | Beauty     |   345 |      1
  1 | Practical Fresh Shirt  | Toys       |   876 |      3
(3 rows)
```
