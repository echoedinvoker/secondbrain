---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Useful Queries across tables with foreign key

For example, if you have a `users` table and a `posts` table, they have one-to-many relationship, where one user can have many posts. So the `posts` table has a foreign key column `user_id` that references the `id` column in the `users` table.

Then, we can start with the foreign key column `user_id` in the `posts` table to create some useful queries across the two tables.


## Case 1: Get all posts by a specific user
```sql
SELECT * FROM photos WHERE user_id = 4; -- get all posts by user with id 4
```

## Case 2: Get all posts content with their user information
```sql
SELECT url, username -- 3. select the columns from both tables
FROM photos -- 1. filter table from database
JOIN users ON photos.user_id = users.id; -- 2. concatenate the two tables by matching the foreign key and primary key
```
