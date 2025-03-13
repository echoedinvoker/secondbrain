---
date: 2025-02-14
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Costs Flow Up

```sql
EXPLAIN SELECT username, contents
FROM users
JOIN comments ON comments.user_id = users.id
WHERE username = 'Alysia17';

--- OUTPUT ---
                                         QUERY PLAN
---------------------------------------------------------------------------------------------
 Hash Join  (cost=8.31..1795.11 rows=11 width=81)
   Hash Cond: (comments.user_id = users.id)
   ->  Seq Scan on comments  (cost=0.00..1628.10 rows=60410 width=72)
   ->  Hash  (cost=8.30..8.30 rows=1 width=17)
         ->  Index Scan using users_username_idx on users  (cost=0.28..8.30 rows=1 width=17)
               Index Cond: ((username)::text = 'Alysia17'::text)
(6 rows)

```

In fact, the startup cost of `Hash Join` is almost zero, but why is the startup cost of `Hash Join` in the example above 8.31?

This is because the startup costs of each child must be added to the startup costs of the parent step.

```py
  (0.28   +   8.02)    +     0.00     +     0.01     =     8.31
#  ^^^^       ^^^^           ^^^^           ^^^^
# index scan  Hash itself   Seq Scan       Hash Join itself has a little startup cost 0.01
# ^^^^^^^^^^^^^^^^^^^^^^^
#        Hash (8.30)
# ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                        Hash Join (8.31)

```

![visualized-plan.png](../assets/imgs/visualized-plan.png)
