---
date: 2025-01-02
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Aggregation function

In [[2025-01-02_Group-and-Aggregation|this topic]], we said that aggregation gets single value through `aggregation function`, here is the list of aggregation functions:

![aggr-list.png](../assets/imgs/aggr-list.png)

Let's try to use them:

```sql
SELECT MAX(photo_id)
FROM comments;

--- OUTPUT ---
 max 
-----
   5
(1 row)

```

![aggregate-to-single.png](../assets/imgs/aggregate-to-single.png)


However, it is important to note that once an aggregation function is used in the SELECT clause, columns cannot be selected again, for example:

```sql
SELECT MAX(photo_id), user_id
FROM comments;

--- OUTPUT ---
psql:/tmp/nvim.mattc/3j3tck/course-query-2025-01-02-14-56-03:2: ERROR:  column "comments.user_id" must appear in the GROUP BY clause or be used in an aggregate function
LINE 1: SELECT MAX(photo_id), user_id
                              ^
```

But using multiple aggregation functions in a SELECT clause is possible:

```sql
SELECT MAX(photo_id), MIN(photo_id)
FROM comments;


 max | min 
-----+-----
   5 |   1
(1 row)

