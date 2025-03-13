---
date: 2025-01-27
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# How an Index Works

```sql
SELECT *
FROM users
WHERE username = 'Riann';
```
Assuming we want to increase the speed of the above query, we can create an index on the `username` column of the `users` table.

![index-on-users-usernam.png](../assets/imgs/index-on-users-usernam.png)
Next, extract the value of the username from each record and at the same time remember the position of this record in the heap file.

![extract-value-and-record-place.png](../assets/imgs/extract-value-and-record-place.png)
Then, sort the extracted values in ascending order.

![sort-the-extracted-values.png](../assets/imgs/sort-the-extracted-values.png)
Organize the sorted values in a *tree* data structure.

![values-to-tree.png](../assets/imgs/values-to-tree.png)

Now, the index is ready. When we run the query, the database engine will first look up the value in the index tree. Then, it will find the position of the record in the heap file and only load the necessary page into memory.

![use-index-to-find-record-place.png](../assets/imgs/use-index-to-find-record-place.png)

You can see that the first leaf node is passed by the root node helpers, so the search in the index tree is very fast. This is how an index works.





