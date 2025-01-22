---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Subqueries in a FROM

```sql
SELECT name, price_weight_ratio
FROM ( ) AS p
--    ^ subquery inserted to FROM clause MUST can accept all shape of data but it'll check the consistency of the data
WHERE price_weight_ratio > 5;

```

So, how to check the **consistency**?

Let's say the subquery is:

```sql
SELECT
  name,
  price / weight AS price_weight_ratio
FROM
  products;

--- OUTPUT ---
            name             | price_weight_ratio 
--          ^^^^               ^^^^^^^^^^^^^^^^^^  the results of subquery have only two columns `name` and `price_weight_ratio` in this case
--                                                 so, any clause of outer query can only use these two columns
-----------------------------+--------------------
 Practical Fresh Shirt       |                292
 Gorgeous Steel Towels       |                 25
 Rustic Plastic Bacon        |                  1
 Tasty Wooden Ball           |                 34
 Fantastic Soft Fish         |                  1
 Gorgeous Concrete Towels    |                 29
 Incredible Granite Mouse    |                494

-- ...

```

Let's insert the subquery into the main query:

```sql
SELECT name, price_weight_ratio
FROM (
  SELECT
    name,
    price / weight AS price_weight_ratio
  FROM
    products
) AS p
WHERE price_weight_ratio > 5;

--- OUTPUT ---  this query works fine because it's consistent with the subquery
            name             | price_weight_ratio 
-----------------------------+--------------------
 Practical Fresh Shirt       |                292
 Gorgeous Steel Towels       |                 25
 Tasty Wooden Ball           |                 34
 Gorgeous Concrete Towels    |                 29
 Incredible Granite Mouse    |                494

-- ...

```

Let's try to break the consistency by adding an extra column in the main query:

```sql
SELECT name, price_weight_ratio, price
--                               ^^^^^ add a column of table `products`
FROM (
  SELECT
    name,
    price / weight AS price_weight_ratio
  FROM
    products
) AS p
WHERE price_weight_ratio > 5;


--- OUTPUT --- this query will fail because it's inconsistent with the subquery, even column `price` is one of the columns of table `products`
--             but the subquery only returns two columns `name` and `price_weight_ratio`, not including `price`
psql:/tmp/nvim.mattc/oqJ78A/big_data-query-2025-01-05-14-35-59:9: ERROR:  column "price" does not exist
LINE 1: SELECT name, price_weight_ratio, price
                                         ^
```

Another thing to note is that if we insert the subquery into FROM clause, the subquery MUST have an alias, in this case `AS p`.

Let's try to remove the alias:

```sql
SELECT name, price_weight_ratio
FROM (
  SELECT
    name,
    price / weight AS price_weight_ratio
  FROM
    products
) -- remove the alias `as p`

WHERE price_weight_ratio > 5;


--- OUTPUT --- this query will fail because the subquery doesn't have an alias
psql:/tmp/nvim.mattc/oqJ78A/big_data-query-2025-01-05-14-35-59:9: ERROR:  subquery in FROM must have an alias
LINE 2: FROM (
             ^
HINT:  For example, FROM (SELECT ...) [AS] foo.

```


