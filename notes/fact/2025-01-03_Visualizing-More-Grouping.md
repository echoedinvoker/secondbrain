---
date: 2025-01-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Visualizing More Grouping

Let's do a query that

> Find the number of comments for each photo.

The first thing we need to think about is what resources this query will touch?

Because there is a `photo_id` column in the `comments` table, this query only needs to touch the `comments` resource, we can group by `photo_id` and count the number of comments for each photo.

```sql
SELECT photo_id, COUNT(*) FROM comments GROUP BY photo_id;

--- OUTPUT ---
 photo_id | count 
----------+-------
        1 |    19
        3 |    25
        5 |    20
        2 |    19
        4 |    17
(5 rows)

```

![more-group1.png](../assets/imgs/more-group1.png)

![more-group2.png](../assets/imgs/more-group2.png)

![more-grouop3.png](../assets/imgs/more-grouop3.png)
