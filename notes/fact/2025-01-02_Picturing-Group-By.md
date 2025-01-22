---
date: 2025-01-02
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Picturing Group By

```sql
SELECT user_id
FROM comments
GROUP BY user_id;  -- we call `user_id` a `grouped column`

--- OUTPUT ---
 user_id 
---------
       1
       3
       5
       4
       2
(5 rows)

```

Let's visualize the `GROUP BY` operation:

![group-by-unique.png](../assets/imgs/group-by-unique.png)

![move-to-group.png](../assets/imgs/move-to-group.png)

With `GROUP BY`, we cannot select original columns anymore except of grouped column (in this case `user_id`).

```sql
SELECT contents
--     ^^^^^^^^ we cannot select this column anymore because we has `GROUP BY` operation below
FROM comments
GROUP BY user_id;

--- OUTPUT ---
psql:/tmp/nvim.mattc/Y8RcAT/course-query-2025-01-02-14-45-33:3: ERROR:  column "comments.contents" must appear in the GROUP BY clause or be used in an aggregate function
LINE 1: SELECT contents
               ^
```

But we can use aggregate functions like `COUNT`, `SUM`, `AVG` to get more information about each group.
