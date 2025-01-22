---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Some notes about UNION

In [[2025-01-04_Handling-Sets-with-UNION|this topic]], we learned about the UNION operator. Here are some notes about it:


## Parenthesis can be omitted sometimes

In previous topics, when we use `UNION`, the main reason we need to use parentheses to wrap two `SELECT` statements is to avoid the database engine from being unclear about the scope of `ORDER BY` and `LIMIT`. If there are only `SELECT` statements, parentheses can be omitted.

```sql
SELECT * FROM products
UNION
SELECT * FROM products;  -- Parentheses can be omitted in this case

--- OUTPUT ---
 id  |            name             | department  | price | weight 
-----+-----------------------------+-------------+-------+--------
  13 | Ergonomic Steel Car         | Outdoors    |    53 |     20
  74 | Small Metal Mouse           | Baby        |    60 |      6
  58 | Licensed Steel Towels       | Industrial  |   939 |     23
   8 | Gorgeous Rubber Ball        | Books       |   801 |      4
  63 | Handcrafted Frozen Shoes    | Sports      |    84 |      1
```

```sql
SELECT * FROM products ORDER BY id DESC
--                     ^^^^^^^^^^^^^^^^
UNION
SELECT * FROM products ORDER BY id;
--                     ^^^^^^^^^^^^  database engine will be unclear about the scope of ORDER BY


--- OUTPUT ---
psql:/tmp/nvim.mattc/fBZfiK/big_data-query-2025-01-04-10-40-32:3: ERROR:  syntax error at or near "UNION"
LINE 2: UNION
        ^
-- so the error will be raised

```

## Query results columns must match

When using `UNION`, the name of columns and their data types must match. If they don't, the database engine will raise an error.

```sql
SELECT name FROM products
--     ^^^^
UNION
SELECT price FROM products;
--     ^^^^^ columns are different


--- OUTPUT ---
psql:/tmp/nvim.mattc/fBZfiK/big_data-query-2025-01-04-10-40-32:3: ERROR:  UNION types character varying and integer cannot be matched
LINE 3: SELECT price FROM products;
               ^
```

## UNION is one of the set operators

Set operators are used to combine the result of two or more `SELECT` statements and `UNION` is one of them. There are two other set operators: `INTERSECT` and `EXCEPT` we will learn about them in the next topics.

