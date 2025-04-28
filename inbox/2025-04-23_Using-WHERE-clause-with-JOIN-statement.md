---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Using WHERE clause with JOIN statement

Of course, we can also use the WHERE clause in the JOIN statement to perform more complex queries. The point is to recognize the order (execution and syntax) of each clause in the SQL statement.

```sql
SELECT 
  url, contents -- 4. filter columns
FROM
  photos -- 1. filter table
JOIN
  comments ON comments.photo_id = photos.id -- 2. join table to get the joined table

-- MUST be after the join clause!!!
WHERE
  comments.user_id = photos.user_id; -- 3. filter records in the joined table
```
