---
date: 2025-01-15
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Checks Over Multiple Columns

In [[2025-01-13_Adding-a-Validation-Check|this topic]], we learned how to add a validation check to a single column. But what if we want to check multiple columns?

Here we assume that we want to create a table `orders` with the columns `name`, `created_at`, and `est_delivery`. We want to make sure that the `est_delivery` is later than the `created_at`.

```sql
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  name VARCHAR(40) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  --                   ^^^^^^^^^^^^^^^^^^^^^^^^^ set the default value to the current timestamp
  est_delivery TIMESTAMP NOT NULL
  CHECK (est_delivery > created_at)  -- we write the CHECK constraint on its own line instead of next to the column
);

--- OUTPUT ---
CREATE TABLE

```

As above, when the CHECK condition involves multiple columns, we write the CHECK condition on separate lines instead of next to the columns.

The current time is `2025-01-15 13:11:47.056792`. Let's try to insert some records into the `orders` table.

```sql
INSERT INTO orders (name, est_delivery)
VALUES ('Shirt', '2025-01-10 13:08:36');
--                ^^^^^^^^^^^^^^^^^^^ the est_delivery is earlier than the created_at

--- OUTPUT --- We got an error because the `est_delivery` is earlier than the `created_at`
psql:/tmp/nvim.mattc/cuxn09/8.sql:10: ERROR:  new row for relation "orders" violates check constraint "orders_check"
DETAIL:  Failing row contains (1, Shirt, 2025-01-15 13:11:47.056792, 2025-01-10 13:08:36).

```

```sql
INSERT INTO orders (name, est_delivery)
VALUES ('Shirt', '2025-01-20 13:08:36');
--                ^^^^^^^^^^^^^^^^^^^ the est_delivery is later than the created_at

--- OUPTUT --- The record is inserted successfully
INSERT 0 1

```
