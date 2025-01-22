---
date: 2025-01-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# More HAVING!

Let's do a query that

> Find the users (user_id) where the user has commented on the first 2 photos
> and the user added more than 2 comments on those photos.

What resources do we need? --> only `comments` table is enough.

**where the user has commented on the first 2 photos** --> WHERE clause
**the user added more than 2 comments on those photos** --> need to count the comments, so aggregate function is needed --> HAVING clause

```sql
SELECT user_id
FROM comments
WHERE photo_id IN (1, 2)
GROUP BY user_id
HAVING COUNT(*) > 2;
--     ^^^^^^^^ even there is no `COUNT(*)` in SELECT clause, we can still use it in HAVING clause.


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


