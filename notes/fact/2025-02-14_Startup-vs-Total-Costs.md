---
date: 2025-02-14
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Startup vs Total Costs

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

cost=8.31..1795.11 is not range of estimated costs.

8.31 is the cost of outputting the very first row, and 1795.11 is the cost of outputting all rows.

cost=8.30..8.30 means this step only outputs when all input rows are processed. (so it'll make all flow slower)

Seq Scan's startup cost is 0.00, there is no idea why it is 0.00, but it is not important for now.

We can visualize above query plan as below:

![visualized-plan.png](../assets/imgs/visualized-plan.png)


**Why we need to know this?**

Because the cost of one step is 8.31..1795.11 (gradually output) or 1795.11..1795.11 (output at once), this difference may affect the execution time of that step.

