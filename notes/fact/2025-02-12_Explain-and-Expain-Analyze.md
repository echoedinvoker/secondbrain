---
date: 2025-02-12
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Explain and Expain Analyze

Before explaining how `Planner` works, we must understand two important commands: `EXPLAIN` and `EXPLAIN ANALYZE`.

```sql
SELECT username, contents
FROM users
JOIN comments ON comments.user_id = users.id
WHERE username = 'Alyson14';


--- OUTPUT --- 
 username |                                  contents                                   
----------+-----------------------------------------------------------------------------
 Alyson14 | Veniam quae tempore et exercitationem molestiae nobis iste inventore earum.
 Alyson14 | Placeat mollitia amet quo dolorem earum suscipit qui consequatur aut.
 Alyson14 | Temporibus sunt soluta corrupti ut quisquam et est harum voluptatem.
 Alyson14 | Porro dolor incidunt animi quia nemo qui necessitatibus dolor reiciendis.
 Alyson14 | Et incidunt inventore quia rerum et recusandae repellat est vel.
 Alyson14 | Aut reprehenderit magnam qui quaerat non omnis ea architecto illum.
 Alyson14 | Ratione impedit laudantium et placeat in dolor optio eligendi iure.
(7 rows)

```

If we add `EXLAIN` before the query, we will get the query plan:

```sql
EXPLAIN SELECT username, contents
FROM users
JOIN comments ON comments.user_id = users.id
WHERE username = 'Alyson14';

--- OUTPUT ---
                                         QUERY PLAN                                          
---------------------------------------------------------------------------------------------
 Hash Join  (cost=8.31..1795.11 rows=11 width=81)
   Hash Cond: (comments.user_id = users.id)
   ->  Seq Scan on comments  (cost=0.00..1628.10 rows=60410 width=72)
   ->  Hash  (cost=8.30..8.30 rows=1 width=17)
         ->  Index Scan using users_username_idx on users  (cost=0.28..8.30 rows=1 width=17)
               Index Cond: ((username)::text = 'Alyson14'::text)
(6 rows)
```

If we add `EXPAIN ANALYZE` before the query, we will get the query plan and the time it took to execute the query:

```sql
EXPLAIN ANALYZE SELECT username, contents
FROM users
JOIN comments ON comments.user_id = users.id
WHERE username = 'Alyson14';


--- OUTPUT ---
                                                              QUERY PLAN                                                               
---------------------------------------------------------------------------------------------------------------------------------------
 Hash Join  (cost=8.31..1795.11 rows=11 width=81) (actual time=0.084..10.391 rows=7 loops=1)
   --                                              ^^^^^^^^^^^^^^^^^^^^^^^^^ time it took to execute the query
   Hash Cond: (comments.user_id = users.id)
   ->  Seq Scan on comments  (cost=0.00..1628.10 rows=60410 width=72) (actual time=0.007..4.679 rows=60410 loops=1)
   ->  Hash  (cost=8.30..8.30 rows=1 width=17) (actual time=0.020..0.021 rows=1 loops=1)
         Buckets: 1024  Batches: 1  Memory Usage: 9kB
         ->  Index Scan using users_username_idx on users  (cost=0.28..8.30 rows=1 width=17) (actual time=0.012..0.013 rows=1 loops=1)
               Index Cond: ((username)::text = 'Alyson14'::text)
 Planning Time: 0.633 ms -- Time it took to plan the query
 Execution Time: 10.441 ms -- Time it took to execute the query
(9 rows)
```

So the difference between `EXPLAIN` and `EXPLAIN ANALYZE` is that `EXPLAIN` only generates a query plan but does not actually execute the query, while `EXPLAIN ANALYZE` generates a query plan, actually executes the query, and displays the execution time.

Even though `EXPLAIN ANALYZE` will execute the query, it will not return the query results, only the query plan and execution time. Therefore, we will not use `EXPLAIN` or `EXPLAIN ANALYZE` in a production environment.




