---
date: 2025-01-08
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# The Greatest Value in a List

PostgreSQL provides a function called `GREATEST` that returns the greatest value in a list of values.

```sql
SELECT GREATEST(20, 1 ,5);

--- OUTPUT ---
 greatest 
----------
      20  -- GREATEST returns the greatest value in the list
(1 row)

```

So, when we need to use it?

Let's say we want to compute the shipping cost of each product, the formula is the following:
> the greater of the product's weight * 2 and $30

This is a perfect use case for `GREATEST`:

```sql
SELECT name, GREATEST(weight * 2, 30) AS shipping_cost
FROM products;

--- OUTPUT ---
            name             | shipping_cost 
-----------------------------+---------------
 Practical Fresh Shirt       |            30  -- when weight * 2 < 30 then 30 is returned
 Gorgeous Steel Towels       |            32  -- when weight * 2 > 30 then weight * 2 is returned
 Rustic Plastic Bacon        |            30
 Tasty Wooden Ball           |            46
 Fantastic Soft Fish         |            30
 Gorgeous Concrete Towels    |            30

-- ...

 Practical Steel Shoes       |            30
 Handcrafted Rubber Shoes    |            30
 Intelligent Cotton Gloves   |            58
(100 rows)

