---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# When our needs involve multiple resources: JOIN

When the columns we need come from different resources, we can use a `JOIN` to combine them. A `JOIN` is a way to combine rows from two or more tables based on related columns between them (usually a foreign key and a primary key, but not always).

For example, if we have a `users` table and an `orders` table, we want to retrieve the user information along with their orders. We can do this by joining the two tables based on the `user_id` column in the `orders` table and the `id` column in the `users` table.

```sql
SELECT users.username, orders.product_name, orders.quantity -- 3. then, we can select the columns we want from both tables
FROM orders -- 1. usually starts with the table that has the foreign key
JOIN users ON orders.user_id = users.id; -- 2. join other table based on the related columns
```

Because we started with the `orders` table, we can omit the `orders.` prefix in the `SELECT` statement.

```sql
SELECT users.username, product_name, quantity
--                     ^^^^^^^^^^^^^^^^^^^^^^ omit the prefix `orders.`, but `users.` is still needed
FROM orders
JOIN users ON orders.user_id = users.id;
```

Some notes about the `JOIN`:

- [[2025-04-23_Order-of-joined-tables|Order of joined tables]]
- [[2025-04-23_If-column-names-collide-between-tables|If column names collide between tables]]
- [[2025-04-23_Using-alias-to-make-statement-with-JOIN-more-concise|Using alias to make statement with JOIN more concise]]
- [[2025-04-23_Feature-of-INNER-JOIN|Feature of INNER JOIN]]
- [[2025-01-01_Four-type-of-join|Four type of join]]
- [[2025-04-23_Using-WHERE-clause-with-JOIN-statement|Using WHERE clause with JOIN statement]]
- [[2025-04-23_Join-more-than-two-tables|Join more than two tables]]
