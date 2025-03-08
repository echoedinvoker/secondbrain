---
date: 2025-02-15
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Quick Solution

We based on the analysis of [[2025-02-14_Common-Table-Expressions|previous topic]] to create a quick solution of SQL script.

First, union the two tables `caption_tags` and `photo_tags` as table `tags` and join it with table `users`.

```sql
SELECT
FROM users
JOIN (
  SELECT user_id, tag_id FROM caption_tags
  --     ^^^^^^^^^^^^^^^
  UNION ALL
  --    ^^^ keep duplicates, not remove them
  SELECT user_id, tag_id FROM photo_tags
  --     ^^^^^^^^^^^^^^^ remember that the union columns must be the same
) AS tags ON tags.users_id = users.id
-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
-- give a name to the union table and join it with users
```

Then, use the `WHERE` clause to filter the result.

```sql
SELECT
FROM users
JOIN (
  SELECT user_id, tag_id FROM caption_tags
  UNION ALL
  SELECT user_id, tag_id FROM photo_tags
) AS tags ON tags.users_id = users.id
WHERE tags.create_at < '2010-01-07'; -- filter the rows
```

Finally, filter the columns to show only the necessary information.

```sql
SELECT username, tags.create_at
--               ^^^^^ we want to show the create_at column of the union table
FROM users
JOIN (
  SELECT user_id, tag_id FROM caption_tags
  UNION ALL
  SELECT user_id, tag_id FROM photo_tags
) AS tags ON tags.users_id = users.id
WHERE tags.create_at < '2010-01-07';
```

So far, this query has been able to achieve our goal, but the only downside is that the subquery part is a bit complicated.

If the subquery part is complicated, it is difficult for other engineers to see at a glance what our entire query is trying to accomplish. We will continue to discuss how to simplify this query in the next topic.





