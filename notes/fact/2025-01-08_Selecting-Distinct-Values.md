---
date: 2025-01-08
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Selecting Distinct Values

Keyword `DISTINCT` is used after `SELECT` to remove duplicate values from the result set. 

```sql
SELECT DISTINCT department
FROM products;

--- OUTPUT ---
 department  
-------------
 Tools
 Electronics
 Sports
 Clothing
 Books
 Kids
 Music
 Garden
 Outdoors
 Toys
 Industrial
 Health
 Grocery
 Movies
 Jewelery
 Home
 Shoes
 Baby
 Games
 Computers
 Beauty
(21 rows)
```

It's like `GROUP BY` but without aggregating the results but rows will be condensed, so we can see each record's other columns such as `price`, `id`, etc. 

We can use multiple columns after `DISTINCT`:

```sql
SELECT DISTINCT department, name
--              ^^^^^^^^^^^^^^^^ it will treat (department, name) as a single value, not two separate columns
FROM products;

--- OUTPUT ---
 department  |            name             
-------------+-----------------------------
 Kids        | Handcrafted Cotton Bacon
--^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 Sports      | Unbranded Wooden Ball
 Beauty      | Small Plastic Soap
 Sports      | Handmade Plastic Gloves
 Movies      | Sleek Soft Computer
 Industrial  | Practical Concrete Sausages
 Shoes       | Awesome Metal Pants
 Kids        | Handcrafted Soft Chicken
--^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  you can see that the Kids appear multiple times, but its name is different
--                                       so it still be considered as a distinct value
 Shoes       | Awesome Cotton Salad
 Sports      | Handcrafted Frozen Shoes
 Outdoors    | Handmade Rubber Salad
 Movies      | Licensed Steel Car
-- ...
 Beauty      | Licensed Plastic Salad
 Beauty      | Tasty Metal Cheese
(100 rows)

```

We also often use `COUNT` and `DISTINCT` together to calculate the number of distinct items.

```sql
SELECT COUNT(DISTINCT department)
FROM products;

--- OUTPUT ---
 count 
-------
    21
(1 row)

```
