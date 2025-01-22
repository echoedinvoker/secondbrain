---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Subqueries in a SELECT

In [[2025-01-05_Thinking-About-the-Structure-of-Data|this topic]], we learned that subqueries can be inserted into many different locations of a query, we want to focus on subqueries in the SELECT clause in this topic.

The decision of whether subqueries can be inserted into the SELECT clause depends on the fact that the return data of the subquery must be a single value.

```sql
SELECT name, price, ( )
--                   ^ if we put a subquery here, it must return a single value
FROM products
WHERE price > 876;

```

```sql
SELECT MAX(price)
FROM products;

--- OUTPUT ---
 max 
-----
991     -- returns a single value, so this query can be inserted into the SELECT clause
        -- no matter it's meaningful or not, sql engine only cares about the data structure(shape)
(1 row)

```

```sql
SELECT name, price, (
  SELECT MAX(price)
  FROM products
)
FROM products
WHERE price > 876;

--- OUTPUT ---
           name            | price | max 
---------------------------+-------+-----
 Incredible Granite Mouse  |   989 | 991
 Generic Fresh Computer    |   926 | 991
 Fantastic Metal Chair     |   887 | 991
 Handcrafted Rubber Towels |   945 | 991
 Practical Rubber Mouse    |   948 | 991
 Handmade Rubber Chicken   |   959 | 991
 Awesome Fresh Keyboard    |   982 | 991
--                                   ^^^ the query just works fine, even if it's not meaningful

-- ...

```

Let's try to insert another subquery:

```sql
SELECT price
FROM products
WHERE id = 3;

--- OUTPUT ---
 price 
-------
    10  -- returns a single value, so this query can be inserted into the SELECT clause
(1 row)

```

```sql
SELECT name, price, (
  SELECT price
  FROM products
  WHERE id = 3
)
FROM products
WHERE price > 876;

--- OUTPUT ---
           name            | price | price 
--                           ^^^^^   ^^^^^ query works fine, but there are two columns named "price"
---------------------------+-------+-------
 Incredible Granite Mouse  |   989 |    10
 Generic Fresh Computer    |   926 |    10
 Fantastic Metal Chair     |   887 |    10
 Handcrafted Rubber Towels |   945 |    10
 Practical Rubber Mouse    |   948 |    10

-- ...

```

We can give an alias to the subquery to avoid the column name conflict:

```sql
SELECT name, price, (
  SELECT price
  FROM products
  WHERE id = 3
) AS price_3
--^^^^^^^^^^ give an alias to the subquery result
FROM products
WHERE price > 876;

--- OUTPUT ---
           name            | price | price_3 
--                                   ^^^^^^^ alias
---------------------------+-------+---------
 Incredible Granite Mouse  |   989 |      10
 Generic Fresh Computer    |   926 |      10
 Fantastic Metal Chair     |   887 |      10
 Handcrafted Rubber Towels |   945 |      10
 Practical Rubber Mouse    |   948 |      10

-- ...


