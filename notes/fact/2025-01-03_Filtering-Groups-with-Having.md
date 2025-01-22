---
date: 2025-01-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Filtering Groups with Having

![clauses-and-having.png](../assets/imgs/clauses-and-having.png)

`HAVING` is similar to `WHERE`, but it is used to filter groups instead of rows. So, it only appears in queries that contain a `GROUP BY` clause.

Let's do a query that:

```py
"Find the number of comments for each photo"

"where the photo_id is less than 3"
# photo_id < 3, this is focused on each row of the table, so we use WHERE

"and the photo has more than 2 comments"
# we need to use COUNT(*) to count the number of comments for each photo
# when using aggregate functions, only the groups can be filtered, so we use HAVING

```

![step-group-having1.png](../assets/imgs/step-group-having1.png)

![step-group-having2.png](../assets/imgs/step-group-having2.png)


```sql
SELECT photo_id, COUNT(*)
FROM comments
WHERE photo_id < 3
GROUP BY photo_id
HAVING COUNT(*) > 2;


--- OUTPUT ---
 photo_id | count 
----------+-------
        1 |    19
        2 |    19
(2 rows)
```
