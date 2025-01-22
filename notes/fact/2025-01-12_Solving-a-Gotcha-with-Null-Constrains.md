---
date: 2025-01-12
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Solving a Gotcha with Null Constrains


## Fixing the Gotcha

In [[2025-01-12_Applying-Null-Constrain|this topic]], we met a Gotcha when applying a null constrain to a column. This topic will show you how to solve it.

To fix it, we need to set a temporary price for the products without a price. 

```sql
UPDATE products
  SET price = 9999
  WHERE price IS NULL;
--            ^^^^^^^ not `= NULL`, but `IS NULL`

--- OUTPUT ---
UPDATE 1

```

Then, make sure that all products have a price.

```sql
SELECT * FROM products;

--- OUTPUT ---
 id | name  | department | price | weight 
----+-------+------------+-------+--------
  1 | Shirt | Cloth      |  9999 |     10
(1 row)

```

Now, we can apply the null constrain to the price column.

```sql
ALTER TABLE products
  ALTER COLUMN price
  SET NOT NULL;

--- OUTPUT ---
ALTER TABLE

```
Let's insert a new product without a price to test the null constrain.

```sql
INSERT INTO products (
  name, department, weight
) VALUES ( 'Pants', 'Clothing', 1.0 );


--- OUTPUT ---
psql:/tmp/nvim.mattc/SEwR95/validation-query-2025-01-12-15-47-10:3: ERROR:  null value in column "price" of relation "products" violates not-null constraint
DETAIL:  Failing row contains (2, Pants, Clothing, null, 1).       --       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                                                   --       It says that we violated the not-null constraint.

```

## Re-create the table `products`

In vest majority of senarios, we'll add null constrain to a column when we create the table. A column without a null constraint is usually the result of careful consideration rather than the norm.

Therefore, we reestablish the `products` table and add null constraints to most columns, while considering from the application's perspective whether each column can allow null values.

```sql
DROP TABLE products;

--- OUTPUT ---
DROP TABLE

```

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  department VARCHAR(50) NOT NULL,
  price INTEGER NOT NULL,
  weight INTEGER -- only weight can be null because sometimes we really don't know the weight of a product but need to insert it.
);


--- OUTPUT ---
CREATE TABLE

```
