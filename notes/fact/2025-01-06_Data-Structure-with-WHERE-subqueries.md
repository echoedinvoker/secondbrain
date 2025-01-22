---
date: 2025-01-06
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Data Structure with WHERE subqueries

In [[2025-01-06_More-Useful:-Subqueries-with-WHERE|this topic]], we learned that the allowed data shape of subqueries in the WHERE clause are dependent on the comparison operator used. 

Here is a summary of the allowed data structures of subqueries in the WHERE clause:

![where-subquery-list.png](../assets/imgs/where-subquery-list.png)

Let's do a practical example to understand how to use above table. Assume we want to write a query that:

> Show the name of all products with a price greater than the average price of all products.

Let's write the outer query first:

```sql
SELECT name
FROM products
WHERE price > ( );
--          ^ the comparison operator is '>', with above table we know that we can use a subquery that returns a single value

```

subquery that returns a single value:

```sql
SELECT AVG(price)
FROM products;

--- OUTPUT ---
         avg          
----------------------
 498.7300000000000000
(1 row)

```

Let's insert the subquery into the outer query:

```sql
SELECT name
FROM products
WHERE price > (
  SELECT AVG(price)
  FROM products
);

--- OUTPUT ---
           name            
---------------------------
 Practical Fresh Shirt
 Tasty Wooden Ball
 Incredible Granite Mouse
 Gorgeous Rubber Ball
 Generic Fresh Computer
 Fantastic Metal Chair
 Licensed Steel Car
 Tasty Metal Cheese
 Handcrafted Rubber Towels
 Intelligent Metal Mouse
 Generic Cotton Pizza
 Unbranded Cotton Tuna
 Sleek Granite Towels
 Practical Rubber Mouse
 Handcrafted Concrete Bike
 Handmade Rubber Chicken
 Awesome Fresh Keyboard
 Generic Fresh Hat
 Tasty Rubber Soap
 Refined Wooden Mouse
 Gorgeous Steel Cheese
 Incredible Granite Bacon
 Intelligent Fresh Ball
 Sleek Rubber Shoes
 Practical Wooden Chips
 Unbranded Granite Shirt
 Gorgeous Plastic Sausages
 Licensed Steel Towels
 Licensed Cotton Sausages
 Ergonomic Fresh Pants
 Intelligent Plastic Car
 Intelligent Cotton Chips
 Sleek Soft Computer
 Fantastic Fresh Shirt
 Generic Fresh Shoes
 Sleek Fresh Gloves
 Handcrafted Soft Chicken
 Fantastic Fresh Chips
 Handcrafted Frozen Chips
 Small Fresh Gloves
 Unbranded Fresh Tuna
 Refined Concrete Pants
 Licensed Plastic Salad
 Fantastic Granite Soap
 Awesome Steel Towels
 Ergonomic Wooden Tuna
 Tasty Rubber Table
 Refined Metal Tuna
 Practical Steel Shoes
(49 rows)
```
