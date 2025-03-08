---
date: 2025-02-19
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Creating a View

View is almost the same as CTE, but View can be reused in different queries, unlike CTE which can only be used in the same query.

We create a View to UNION the tables `caption_tags` and `photo_tags`.

```sql
CREATE VIEW tags AS (
  SELECT id, created_at, user_id, post_id, 'photo_tag' AS type FROM photo_tags
  UNION ALL
  SELECT id, created_at, user_id, post_id, 'caption_tag' AS type FROM caption_tags
);

--- OUTPUT ---
CREATE VIEW
```

Now we can use the fake table `tags` in any other query.

Let's check the content of the `tags` view.

```sql
SELECT * FROM tags;



--- OUTPUT ---
  id   |         created_at         | user_id | post_id |    type     
-------+----------------------------+---------+---------+-------------
     1 | 2015-06-21 07:39:01.228+00 |    5031 |    8304 | photo_tag
     2 | 2017-06-22 14:29:06.038+00 |     305 |    4171 | photo_tag
     3 | 2016-09-10 11:33:09.269+00 |    3604 |    8953 | photo_tag
...
...
     1 | 2010-11-29 17:20:56.455+00 |    3598 |   12271 | caption_tag
     2 | 2014-04-07 11:51:35.174+00 |    2630 |    8730 | caption_tag
     3 | 2014-04-03 12:36:37.929+00 |    4403 |    9194 | caption_tag
...
...
 19494 | 2012-10-08 11:41:44.019+00 |    4967 |    5023 | caption_tag
 19495 | 2013-01-26 12:01:06.977+00 |    2890 |    1602 | caption_tag
(35397 rows)
```

Now, we can also use that view to solve the problem of [[2025-02-19_Most-Popular-Users|the previous example]].

```sql
SELECT username, COUNT(*)
FROM users
JOIN tags ON tags.user_id = users.id
GROUP BY username
ORDER BY COUNT(*) DESC;


--- OUTPUT ---
         username         | count 
--------------------------+-------
 Kristofer.Torphy         |    19
 Ramiro.Bartoletti92      |    18
 Noble21                  |    17
 Marco.Pfannerstill       |    16
 Brooke_Donnelly89        |    16
...
...
 Bryon_Robel67            |     1
 Berta.Lang               |     1
(5341 rows)

```
