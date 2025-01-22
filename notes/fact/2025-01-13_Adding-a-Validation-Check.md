---
date: 2025-01-13
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Adding a Validation Check

If we want the column `price` to be greater than 0, we can do it like this:

Add a validation check when creating the table:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  department VARCHAR(50) NOT NULL,
  price INTEGER CHECK (price > 0),
  --            ^^^^^^^^^^^^^^^^^ the value inside the parentheses is the condition
  --                              only pass when condition is true
  weight INTEGER
);

```

Or add a validation check to an existing table:

```sql
ALTER TABLE products
ADD CHECK (price > 0);

--- OUTPUT ---
ALTER TABLE

```

```sql
INSERT INTO products (name, department, price)
VALUES ('Mouse', 'Computer', -99);

--- OUTPUT ---
psql:/tmp/nvim.mattc/0mhhIy/validation-query-2025-01-13-14-33-54:2: ERROR:  new row for relation "products" violates check constraint "products_price_check"
DETAIL:  Failing row contains (7, Mouse, Computer, -99, null).
--                                                 ^^^ condition (price > 0) is not met

```

Be careful that the check only works when adding a new row or updating an existing row.


