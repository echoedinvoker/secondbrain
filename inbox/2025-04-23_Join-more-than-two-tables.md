---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Join more than two tables

A JOIN clause can only connect two tables, so if you want to connect more than two tables, you need to use multiple JOIN clauses.

If the three fields come from different tables.

```sql
SELECT url, contents, username -- if three columns come from different tables
                               -- so now we need to join three tables
FROM comments
JOIN photos ON photos.id = comments.photo_id -- we joined the photos and comments tables
                                             -- and got a joined table

-- then, we write another JOIN clause to join the joined table with the users table
-- if both `comments` and `photos` tables have a `user_id` column
-- we need to set them to equal to the users.id 
JOIN users ON users.id = comments.user_id AND users.id = photos.user_id -- join the above joined table with the users table

```

It should be noted that if there are multiple columns in the joined table that are related to the table to be joined later, they must be written in the ON clause, otherwise an error will occur.
