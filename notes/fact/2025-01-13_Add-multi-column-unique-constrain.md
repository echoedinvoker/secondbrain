---
date: 2025-01-13
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Add multi column unique constrain

In this [[2025-01-13_Applying-a-Unique-Constraint-to-One-column|topic]], we learned how to apply a unique constraint to a single column.

```sql
ALTER TABLE products
ADD UNIQUE (name);
--         ^    ^ why need to use parenthesis in syntax?

```

The reason for syntax using parentheses is actually because you can simultaneously add a unique constraint to multiple columns as follows:

```sql
ALTER TABLE products
ADD UNIQUE (name, department);

--- OUTPUT ---
ALTER TABLE

```

Now, only both the `name` and `department` columns combined can have unique values.
```sql
INSERT INTO products
  (name, department)
VALUES
  ('Legos', 'Child'),
  ('Shirt', 'Child');

--- OUTPUT ---
INSERT 0 2
```

```sql
SELECT * FROM products;

 id |    name     | department | price | weight 
----+-------------+------------+-------+--------
  1 | Legos       | Toys       |   999 |      2
  2 | Small Legos | Tools      |   100 |      1
  4 | Legos       | Child      |   999 |       
--    ^^^^^ only name duplicated, so it's okay
  5 | Shirt       | Child      |   999 |       
--                  ^^^^^ only department duplicated, so it's okay
(4 rows)

```

```sql
INSERT INTO products
  (name, department)
VALUES
  ('Shirt', 'Child');

--- OUTPUT
psql:/tmp/nvim.mattc/Yvw4n1/validation-query-2025-01-13-14-01-06:4: ERROR:  duplicate key value violates unique constraint "products_name_department_key"
DETAIL:  Key (name, department)=(Shirt, Child) already exists.
--       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
--       ('Shirt', 'Child') combination already exists, so it's not allowed

```
