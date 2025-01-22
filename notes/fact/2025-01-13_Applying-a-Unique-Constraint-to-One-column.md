---
date: 2025-01-13
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Applying a Unique Constraint to One column

```sql
SELECT * FROM products;

--- OUTPUT ---
 id | name  | department | price | weight 
----+-------+------------+-------+--------
  1 | Legos | Toys       |   999 |      2
(1 row)
```

```sql
INSERT INTO products (
  name, department, price, weight
) VALUES ( 'Legos', 'Tools', 100, 1 )

--- OUTPUT ---
INSERT 0 1

```
```sql
SELECT * FROM products;

--- OUTPUT ---
 id | name  | department | price | weight 
----+-------+------------+-------+--------
  1 | Legos | Toys       |   999 |      2
  2 | Legos | Tools      |   100 |      1
--    ^^^^^ duplicated name value
(2 rows)
```

If you want to prevent duplicate values in a column, you can add a unique constraint to that column. Here's how you can do that:

You can add a unique constraint to a column when you create a table:
```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL UNIQUE,
  --                        ^^^^^^ added unique constraint
  --               ^^^^^^^^^^^^^^^ one column can add multiple constraints
  department VARCHAR(50) NOT NULL,
  price INT DEFAULT 999,
  weight INT
);
```
Or you can add a unique constraint to an existing table:

```sql
ALTER TABLE products
ADD UNIQUE (name);
--^^^^^^^^^^^^^^^^^ added unique constraint, not SET but ADD, it's different from adding null constraint

--- OUTPUT ---
psql:/tmp/nvim.mattc/lhFAlo/validation-query-2025-01-13-13-38-19:2: ERROR:  could not create unique index "products_name_key"
DETAIL:  Key (name)=(Legos) is duplicated.
--       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ like null constraint, we need to remove any existing duplicated values first

```

We can delete all rows with duplicated name or change values to make them unique, here we choose the latter:

```sql
UPDATE products
SET name = 'Small Legos'
WHERE id = 2;

```
Then, we can add the unique constraint to name column:

```sql
ALTER TABLE products
ADD UNIQUE (name);

--- OUTPUT ---
ALTER TABLE

```

Now, we try to insert a duplicated value again:

```sql
INSERT INTO products (
  name, department, price, weight
) VALUES ( 'Legos', 'Tool', 10, 1 )

--- OUTPUT ---
psql:/tmp/nvim.mattc/lhFAlo/validation-query-2025-01-13-13-38-19:4: ERROR:  duplicate key value violates unique constraint "products_name_key"
DETAIL:  Key (name)=(Legos) already exists.
--       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ unique constraint prevents the insertion of duplicated value in name column

```
