---
date: 2025-01-13
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# How to drop unique constrain

In [[2025-01-13_Applying-a-Unique-Constraint-to-One-column|this topic]], we added a unique constraint to the `name` column of the `products` table. Now, let's see how we can drop this constraint.

```sql
   SELECT conname AS constraint_name, 
          contype AS constraint_type,
          pg_get_constraintdef(c.oid) AS constraint_definition
   FROM pg_constraint c
   JOIN pg_namespace n ON n.oid = c.connamespace
   JOIN pg_class t ON t.oid = c.conrelid
   WHERE t.relname = 'products'
     AND n.nspname = 'public';  -- 假设表在 public schema 中

--- OUTPUT ---
  constraint_name  | constraint_type | constraint_definition 
-------------------+-----------------+-----------------------
 products_pkey     | p               | PRIMARY KEY (id)
 products_name_key | u               | UNIQUE (name)
(2 rows)
```

```sql
ALTER TABLE products
DROP CONSTRAINT products_name_key;

--- OUTPUT ---
ALTER TABLE

```

Now, the unique constraint on the `name` column has been removed.


## We can also get constraint name when getting error

For example:

```sql
INSERT INTO products (name, department, price)
VALUES ('Mouse', 'Computer', -99);

--- OUTPUT ---
psql:/tmp/nvim.mattc/0mhhIy/validation-query-2025-01-13-14-33-54:2: ERROR:  new row for relation "products" violates check constraint "products_price_check"
--                                                                                                                                    ^^^^^^^^^^^^^^^^^^^^^^
--                                                                                                           this is the constraint name that just caused the error
--                                                                                                           we can choose to drop it directly if we want
DETAIL:  Failing row contains (7, Mouse, Computer, -99, null).

```
