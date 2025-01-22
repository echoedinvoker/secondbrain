---
date: 2025-01-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# The Not In Operator with a List

Another example of using subqueries in WHERE clauses, and this time we're using the `NOT IN` operator.

Let's do a query that:

> Show the name of all products that are not in the same department as products with a price less than 100.

Write the outer query first:

```sql
SELECT name
FROM products
WHERE department NOT IN ( );
--                       ^ subquery goes here, and it should return a list (single column table)

```

Write the inner query:

```sql
SELECT department
FROM products
WHERE price < 100;

--- OUTPUT --
 department  
-------------
 Movies
 Tools
 Outdoors
 Industrial
 Electronics
 Sports
 Baby
 Baby
 Garden
 Home
 Beauty
 Industrial
(12 rows)
```

Insert the inner query into the outer query:

```sql
-- outer query
SELECT name
FROM products
WHERE department NOT IN (
  -- insert inner query here
  SELECT department
  FROM products
  WHERE price < 100
);

--- OUTPUT ---
            name            
----------------------------
 Practical Fresh Shirt
 Gorgeous Concrete Towels
 Gorgeous Rubber Ball
 Generic Fresh Computer
 Ergonomic Metal Pizza
 Intelligent Metal Mouse
 Awesome Cotton Salad
 Intelligent Cotton Chips
 Unbranded Cotton Tuna
 Practical Plastic Towels
 Practical Wooden Shoes
 Sleek Granite Towels
 Handcrafted Concrete Bike
 Licensed Frozen Chair
 Fantastic Steel Chicken
 Refined Wooden Mouse
 Incredible Granite Bacon
 Handcrafted Fresh Sausages
 Handmade Plastic Fish
 Handcrafted Cotton Bacon
 Sleek Rubber Shoes
 Practical Wooden Chips
 Unbranded Granite Shirt
 Awesome Steel Mouse
 Fantastic Cotton Shirt
 Small Concrete Pants
 Intelligent Plastic Car
 Licensed Steel Towels
 Generic Fresh Shoes
 Sleek Fresh Gloves
 Handcrafted Soft Chicken
 Awesome Metal Pants
 Handcrafted Frozen Chips
 Rustic Metal Salad
 Licensed Soft Chicken
 Fantastic Wooden Chair
 Tasty Rubber Table
 Refined Metal Tuna
 Practical Steel Shoes
(39 rows)

