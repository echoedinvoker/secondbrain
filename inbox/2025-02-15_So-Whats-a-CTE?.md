---
date: 2025-02-15
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# So Whats a CTE?

```sql
SELECT username, tags.created_at
FROM users
JOIN (
  SELECT user_id, created_at FROM caption_tags
  UNION ALL
  SELECT user_id, created_at FROM photo_tags
) AS tags ON tags.users_id = users.id
WHERE tags.created_at < '2010-01-07';
```

We can use a Common Table Expression (CTE) to simplify the query above.

```sql
-- Define a CTE named tags
WITH tags AS (
  SELECT user_id, created_at FROM caption_tags
  UNION ALL
  SELECT user_id, created_at FROM photo_tags
)

SELECT username, tags.created_at
FROM users
JOIN tags ON tags.users_id = users.id
--   ^^^^ now the subquery is replaced by the CTE table directly
--        we just use it as a regular table
WHERE tags.created_at < '2010-01-07';
```

As shown above, we simply extract the part of the subquery and define it as a table `tags` at the beginning using the `WITH` keyword, so that our query becomes more concise and readable.

These two queries are equivalent in terms of results, we can use `EXPLAIN` to see the execution plan of the two queries and compare them.

```sql
EXPLAIN SELECT username, tags.created_at
FROM users
JOIN (
  SELECT user_id, created_at FROM caption_tags
  UNION ALL
  SELECT user_id, created_at AS tag_id FROM photo_tags
) AS tags ON tags.user_id = users.id
WHERE tags.created_at < '2010-01-07';

--- OUTPUT ---
                                          QUERY PLAN                                           
-----------------------------------------------------------------------------------------------
 Hash Join  (cost=736.80..928.96 rows=67 width=21)
   Hash Cond: (users.id = caption_tags.user_id)
   ->  Seq Scan on users  (cost=0.00..171.45 rows=5345 width=17)
   ->  Hash  (cost=735.96..735.96 rows=67 width=12)
         ->  Append  (cost=0.00..735.96 rows=67 width=12)
               ->  Seq Scan on caption_tags  (cost=0.00..386.39 rows=65 width=12)
                     Filter: (created_at < '2010-01-07 00:00:00+00'::timestamp with time zone)
               ->  Seq Scan on photo_tags  (cost=0.00..349.24 rows=2 width=12)
                     Filter: (created_at < '2010-01-07 00:00:00+00'::timestamp with time zone)
(9 rows)
```
```sql
EXPLAIN WITH tags AS (
  SELECT user_id, created_at FROM caption_tags
  UNION ALL
  SELECT user_id, created_at AS tag_id FROM photo_tags
)

SELECT username, tags.created_at
FROM users
JOIN  tags ON tags.user_id = users.id
WHERE tags.created_at < '2010-01-07';

--- OUTPUT ---
                                          QUERY PLAN                                           
-----------------------------------------------------------------------------------------------
 Hash Join  (cost=736.80..928.96 rows=67 width=21)
   Hash Cond: (users.id = caption_tags.user_id)
   ->  Seq Scan on users  (cost=0.00..171.45 rows=5345 width=17)
   ->  Hash  (cost=735.96..735.96 rows=67 width=12)
         ->  Append  (cost=0.00..735.96 rows=67 width=12)
               ->  Seq Scan on caption_tags  (cost=0.00..386.39 rows=65 width=12)
                     Filter: (created_at < '2010-01-07 00:00:00+00'::timestamp with time zone)
               ->  Seq Scan on photo_tags  (cost=0.00..349.24 rows=2 width=12)
                     Filter: (created_at < '2010-01-07 00:00:00+00'::timestamp with time zone)
(9 rows)
```

We've proven that the two queries are equivalent in terms of results and performance.

But there are two type of CTEs: recursive and non-recursive. The above example is a non-recursive CTE, which just to simplify the query.
However, Recursive CTEs can write recursive queries that cannot be written using native SQL, which we will discuss in later topics.




