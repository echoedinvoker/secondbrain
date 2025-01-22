---
date: 2025-01-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Probably Too Much About Correlated Subqueries

![subquery-as-nested-loop.png](../assets/imgs/subquery-as-nested-loop.png)

In a nested loop, the inner loop can use variables from the outer loop, so we can use columns from the outer query in the WHERE clause of the subquery.

![correlated-query.png](../assets/imgs/correlated-query.png)


Let's try to write the query in real codes:

outer query:

```sql
SELECT name, department, price
FROM products
WHERE price = ( );
--             ^ subquery that returns the greatest price in the same department 
--                                                               ^^^^^^^^^^^^^^^ which means subquery need to reference the outer query column `department`

```

subquery:
```sql
SELECT MAX(price)
FROM products
WHERE department = 
--                 ^^^^^ department from outer query

```

Insert the subquery into the outer query:

```sql
SELECT name, department, price
FROM products AS o
--            ^^^^ alias for outer query table for passing its columns to subquery
WHERE price = (
  -- insert subquery here
  SELECT MAX(price)
  FROM products
  WHERE department = o.department
  --                 ^^^^^^^^^^^^ reference to outer query column
);

--- OUTPUT ---
           name            | department  | price 
---------------------------+-------------+-------
 Gorgeous Steel Towels     | Outdoors    |   412
 Gorgeous Concrete Towels  | Grocery     |   328
 Incredible Granite Mouse  | Home        |   989
 Gorgeous Rubber Ball      | Books       |   801
 Handcrafted Rubber Towels | Baby        |   945
 Practical Plastic Towels  | Games       |   379
 Handmade Rubber Chicken   | Movies      |   959
 Tasty Rubber Soap         | Tools       |   823
 Incredible Granite Bacon  | Music       |   982
 Handmade Granite Fish     | Electronics |   166
 Licensed Steel Towels     | Industrial  |   939
 Fantastic Cotton Shirt    | Health      |   496
 Licensed Cotton Sausages  | Sports      |   751
 Intelligent Plastic Car   | Shoes       |   628
 Sleek Fresh Gloves        | Clothing    |   919
 Handcrafted Soft Chicken  | Kids        |   720
 Small Fresh Gloves        | Garden      |   991
 Licensed Plastic Salad    | Beauty      |   834
 Tasty Rubber Table        | Computers   |   525
 Refined Metal Tuna        | Jewelery    |   708
 Practical Steel Shoes     | Toys        |   947
(21 rows)  --                ^^^^^^^^ each department should only appear once

```


## Think About Performance waste of correlated subqueries

In the example of the query above, when the WHERE clause of the outer query checks multiple records with the same department, the inner query will be executed multiple times with the exact same query. This seems to cause a waste of performance, and there should be a smarter solution.

