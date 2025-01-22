---
date: 2025-01-08
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# And the Least Value in a List!

Very similar to [[2025-01-08_The-Greatest-Value-in-a-List|The Greatest Value in a List]], now we want to learn another function provided by PostgreSQL - `LEAST`.

We directly see a real world case this time!

Let's say we want to do a query that

> compute sale price of each product, the formula is the least of product's price * 0.5 or $400.

```sql
SELECT name, LEAST(price * 0.5, 400) AS sale_price
FROM products;


--- OUTPUT ---
            name             | sale_price 
-----------------------------+------------
 Practical Fresh Shirt       |        400  -- when product's price * 2 > 400, return 400
 Gorgeous Steel Towels       |      206.0 -- when product's price * 2 < 400, return price * 2
 Rustic Plastic Bacon        |        5.0
 Tasty Wooden Ball           |      398.0
 Fantastic Soft Fish         |        5.0
 Gorgeous Concrete Towels    |      164.0
 Incredible Granite Mouse    |        400

-- ...

 Intelligent Rubber Chicken  |        0.5
 Practical Steel Shoes       |        400
 Handcrafted Rubber Shoes    |      137.5
 Intelligent Cotton Gloves   |      223.5
(100 rows)
```
