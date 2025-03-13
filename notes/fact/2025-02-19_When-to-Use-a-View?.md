---
date: 2025-02-19
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# When to Use a View?

Apart from the initial design mistakes in tables that make us want to use Views, it is also a good time to use Views when the same subquery is needed in many different queries.

For example, below queries are using the same subquery:
- Show the users who created the 10 most recent posts.
- Show the users who were tagged in the 10 most recent posts.
- Show the average number of hashtags used in the 10 most recent posts.
- Show the number of likes each of the 10 most recent posts received.
- Show the hashtags used by the 10 most recent posts.
- Show the total number of comments the 10 most recent posts received.

You can see that the queries above all need to retrieve the 10 latest posts. At this time, you can extract this subquery and write it as a View, so we don't need to write the same subquery in each query above.

```sql
CREATE VIEW recent_posts AS (
    SELECT *
    FROM posts
    ORDER BY created_at DESC
    LIMIT 10
)

--- OUTPUT ---
CREATE VIEW
```

Then, you can use this View in the queries above. Let's use the first query as an example:

```sql
SELECT username
FROM recent_posts
JOIN users ON users.id = recent_posts.user_id;

--- OUTPUT ---
      username       
---------------------
 Hyman_Weimann
 Danny_Mann
 Macie.Bode35
 Lance.Murphy
 Dimitri_Armstrong83
 Damion.Mayert29
 Alessia.Schoen
 Roberta_Osinski
 Jeffrey42
 Marquise_King
(10 rows)

```



