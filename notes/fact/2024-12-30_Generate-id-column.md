---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Generate id column

![users-table-id.png](../assets/imgs/users-table-id.png)

Let's say we want to create a new table as above.

We've learned how to create a table in [[2024-12-30_Create-Table|this topic]], but if we simply set the `id` column as `INTEGER` type, we have to manually increment the value of the `id` column every time we insert a new row to make it unique. It's a pain in the neck.

Hopefully, PostgreSQL provides a type called `SERIAL` which automatically increments the value of the column so that when we insert a new row, we don't need to provide the value of the `id` column at all.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  -- ^^^^^^ ^^^^^^^^^^^ it make PostgreSQL understand this column is primary key so that the performance of the query can be improved.
  -- ^^^^^^ this type automatically increments the value of the column so that we don't need to provide the value of this column when inserting a new row.
  username VARCHAR(50)
);

---- Output ----
CREATE TABLE
```

```sql
INSERT INTO users (username) -- don't need to provide the value of the `id` column
VALUES
  ('monahan93'),
  ('pferrer'),
  ('si93onis'),
  ('99stroman');


---- Output ----
INSERT 0 4
```
