---
date: 2025-02-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Downsides of Indexes

After [[2025-01-29_Benchmarking-Queries|Benchmarking Queries]], many people are tempted to add indexes to all the columns they are querying. However, indexes are not a silver bullet and can have downsides.

**1. Indexes take up space.**

We know that when we add an index to a column, we are adding a tree structure, it's not only in the memory but also in the disk. Let's check the size of the indexes in our disk:

```sql
SELECT pg_size_pretty(pg_relation_size('users'));

--- OUTPUT ---
 pg_size_pretty 
----------------
 944 kB
(1 row)
```

```sql
SELECT pg_size_pretty(pg_relation_size('users_username_idx'));

--- OUTPUT ---
 pg_size_pretty 
----------------
 184 kB
(1 row)
```

So, if your table takes 10GB, and indexes will take 2GB, it costs you 20% more money if you host your database in the cloud.


**2. Slows down inserts, updates, and deletes.**

When you insert a new row, the database has to update the indexes. If you have a lot of indexes, it will take more time to insert a new row.


**3. Index might not actually get used by PostgreSQL in some queries.**

In some case, using an index may not improve performance, and may even decrease performance, so PostgreSQL may choose not to use an index.

If the indexes you have created are not being used, then it is simply a waste of storage and cost.

So, setting up indexes does not garentee that your queries will be faster!



