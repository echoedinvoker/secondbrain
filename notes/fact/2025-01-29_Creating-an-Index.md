---
date: 2025-01-29
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Creating an Index

In [[2025-01-27_How-an-Index-Works|How an Index Works]], we learned about the concept of an index and how it can help us quickly find information in a book. Now, let's learn how to create an index.

```sql
CREATE INDEX ON users (username);
--              ^^^^^  ^^^^^^^^
--              table   column

--- OUTPUT ---
CREATE INDEX
```

Then, we can check the index in the table `pg_indexes`:

```sql
SELECT * FROM pg_indexes where tablename='users' and schemaname='public'

--- OUTPUT ---
 schemaname | tablename |     indexname      | tablespace |                                indexdef                                
------------+-----------+--------------------+------------+------------------------------------------------------------------------
 public     | users     | users_pkey         |            | CREATE UNIQUE INDEX users_pkey ON public.users USING btree (id)
 public     | users     | users_username_idx |            | CREATE INDEX users_username_idx ON public.users USING btree (username)
 --                       ^^^^^^^^^^^^^^^^^^ this is the convention for naming indexes {table}_{column}_idx
(2 rows)
```

If we want to name the index ourselves, we can do so by specifying the name after the `INDEX` keyword:

```sql
CREATE INDEX username_index ON users (username);
--           ^^^^^^^^^^^^^^
```

But it is still recommended to let the system name it, so when deleting, you don't have to remember how you named it originally.

```sql
DROP INDEX users_username_idx;

--- OUTPUT ---
DROP INDEX
```

