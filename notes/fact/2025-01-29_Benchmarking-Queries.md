---
date: 2025-01-29
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Benchmarking Queries

In [[2025-01-29_Creating-an-Index|Creating an Index]], we learned how to create an index on a column of a table. But how do we know if the index is actually helping?

Some clients will show the spend time of the query, but that is not always accurate because it includes the time to send the data back to the client. We need to know how long the query takes to run on the database server.

To do this, we can add `EXPAIN ANALYZE` before the `SELECT` statement. This will show us how long the query takes to run on the database server.

```sql
    EXPLAIN ANALYZE SELECT *
--  ^^^^^^^^^^^^^^^
    FROM users
    WHERE username = 'Emil30';


--- OUTPUT ---
 Seq Scan on users  (cost=0.00..184.81 rows=1 width=202) (actual time=0.076..0.360 rows=1 loops=1)
   Filter: ((username)::text = 'Emil30'::text)
   Rows Removed by Filter: 5344
 Planning Time: 7.238 ms
 Execution Time: 0.383 ms -- this is what we are looking for
(5 rows)
```
Each time the same query is executed, there will be a different `Execution Time`. We execute multiple times to capture an approximate average time.
0.666 ms, 0.383 ms, 0.549 ms, 0.268 ms, 0.719 ms, 0.327 ms -----> Average = 0.466 ms

Now we add index on `username` column and run the same query again.

```sql
CREATE INDEX ON users (username);
```

```sql
    EXPLAIN ANALYZE SELECT *
    FROM users
    WHERE username = 'Emil30';


--- OUTPUT ---
 Index Scan using users_username_idx on users  (cost=0.28..8.30 rows=1 width=202) (actual time=0.030..0.031 rows=1 loops=1)
   Index Cond: ((username)::text = 'Emil30'::text)
 Planning Time: 0.364 ms
 Execution Time: 0.055 ms -- obviously faster
(4 rows)
```

Running the same query multiple times to capture an approximate average time.
0.055 ms, 0.032 ms, 0.052 ms, 0.066 ms, 0.065 ms, 0.028 ms -----> Average = 0.048 ms


Approximately ten times the speed difference ._.



