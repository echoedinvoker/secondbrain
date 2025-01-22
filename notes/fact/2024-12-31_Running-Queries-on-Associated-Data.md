---
date: 2024-12-31
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Running Queries on Associated Data

We've [[2024-12-30_Generate-id-column|created users table]] and [[2024-12-31_Generate-foreign-key-column|photos table]], the letter has a foreign key column that references the id column of the former.

So we can now run some meaningful queries on the associated data.

## Case 1: Find all photos that belong to a user

Assuming we have a user with id 4, we want to find all photos that belong to this user.

```sql
SELECT * FROM photos WHERE user_id = 4;  -- table `photos` already has the information about the owner of the photo in the `user_id` column
                                         -- so we don't even need to use the table `users` here, only the `photos` table is enough


--- Output ---
 id |       url       | user_id 
----+-----------------+---------
  1 | http://img1.jpg |       4
  2 | http://img2.jpg |       4
(2 rows)

```


## Case 2: Find all photos' url and their owner's name

url and name are stored in different tables, so we need to join two tables to get the desired result.

```sql
SELECT url, username  -- third, filter columns
FROM photos  -- first, filter table
JOIN users ON photos.user_id = users.id;  -- second, join table `users` using the foreign key column `user_id` of the `photos` table


--- Output ---
       url       | username  
-----------------+-----------
 http://img3.jpg | monahan93
 http://img4.jpg | pferrer
 http://img2.jpg | 99stroman
 http://img1.jpg | 99stroman
(4 rows)

```

`JOIN` is a most powerful feature in SQL and most hard to master. Above we just scratched the surface, we'll dive deeper in the future.


## Conclusion

From the above examples, you should find that all queries related to associated data are always started with the table that contains the foreign key column... You better have this mindset when you're writing queries on associated data.
