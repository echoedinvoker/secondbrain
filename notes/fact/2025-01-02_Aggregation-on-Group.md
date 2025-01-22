---
date: 2025-01-02
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Aggregation on Group

In [[2025-01-02_Aggregation-function|this topic]], we simply aggregated the data and it just works on the whole dataset.

But if we use aggregation function with group by clause, it will work on each sub-group of the dataset instead of the whole dataset.

```sql
SELECT user_id, MAX(id)
--     ^^^^^^^  ^^^^^^^ with group by clause, we cannot select original columns but can use aggregate functions
--     ^^^^^^^ usually we'll select grouped column to differentiate the groups
FROM comments
GROUP BY user_id; -- aggregate function MAX() will apply on each group of user_id separately

--- Output ---
 user_id | max 
---------+-----
       1 |  79
       3 | 100
       5 |  99
       4 |  96
       2 |  91
(5 rows)

```
![group-of-aggregate.png](../assets/imgs/group-of-aggregate.png)


However, id is not really a number, so getting the maximum id doesn't make sense. We can use another aggregate function like COUNT() for counting the number of comments per user, which makes more sense.

```sql
SELECT user_id, COUNT(id)
--              ^^^^^^^^^^ COUNT() will count the number of comments per user,
--                         if you only want to count the number of rows, you can use COUNT(*)
FROM comments
GROUP BY user_id;

--- Output ---
 user_id | count 
---------+-------
       1 |    23 -- user_id 1 has 23 comments, which makes sense in real world
       3 |    17
       5 |    20
       4 |    22
       2 |    18
(5 rows)
```
