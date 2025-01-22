---
date: 2025-01-13
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Default Column Values

In [[2025-01-12_Applying-Null-Constrain|this topic]], we leaned about how to apply a null constraint to a column. But sometimes, we just don't know the value of a column yet and still want to insert a value for it. In this case, we can use a default column value. 

We can set a default value for a column when creating a table, like this:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  department VARCHAR(50) NOT NULL,
  price INTEGER DEFAULT 999,
  --            ^^^^^^^^^^^ be careful with the type of value should be
  --                        the same as the column type
  weight INTEGER
);
```

Or we can alter an existing table to add a default value to a column:

```sql
ALTER TABLE products
ALTER COLUMN price
SET DEFAULT 999;

--- OUTPUT ---
ALTER TABLE

```

Now, if we insert a new row without specifying the price, the default value will be used:

```sql
INSERT INTO products (
  name, department, weight
) VALUES ( 'Legos', 'Toys', 2 );

--- OUTPUT ---
INSERT 0 1

```

Let's check the inserted row:

```sql
SELECT * FROM products;

--- OUTPUT ---
 
 id | name  | department | price | weight 
----+-------+------------+-------+--------
  1 | Legos | Toys       |   999 |      2
--                           ^^^ default value
(1 row)
```

