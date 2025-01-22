---
date: 2025-01-12
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Applying Null Constrain

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50),
  department VARCHAR(50),
  price INTEGER,
  weight INTEGER
);

--- OUTPUT ---
CREATE TABLE
```

```sql
INSERT INTO products
  (name, department, weight)
VALUES
  ('Shirt', 'Cloth', 10);

--- OUTPUT ---
INSERT 0 1
```


```sql
SELECT * FROM products;

--- OUTPUT ---
 id | name  | department | price | weight 
----+-------+------------+-------+--------
  1 | Shirt | Cloth      |       |     10
--                        ^^^^^^^ if value is not defined when inserting, it's default to NULL, which means nothing
(1 row)
```

If we don't want to allow NULL values in the `price` column, we can add **Null Constrain** to it.

We can add it when creating the table:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50),
  department VARCHAR(50),
  price INTEGER NOT NULL,
  --            ^^^^^^^^
  weight INTEGER
);
```

Or we can add it to an existing table by ALTERing it:

```sql
ALTER TABLE products
ALTER COLUMN price -- column name
SET NOT NULL;
--  ^^^^^^^^ use SET to add the constrain NOT NULL

--- OUTPUT ---
psql:/tmp/nvim.mattc/otNpnz/validation-query-2025-01-12-09-14-59:3: ERROR:  column "price" of relation "products" contains null values

-- this error means that there are already NULL values in the column, so we can't add the constrain

```

Then, we have multiple options to fix that error:
1. find all rows with NULL values and delete them
2. find all rows with NULL values and update them to a temporary price
3. find all rows with NULL values one by one and give each of them a specific price
