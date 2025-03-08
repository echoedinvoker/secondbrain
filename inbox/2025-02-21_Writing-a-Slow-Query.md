---
date: 2025-02-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Writing a Slow Query

We will write the diagram of the three way join (LEFT JOIN) from [[2025-02-20_Reminder-on-Left-Joins|the previous topic]] into SQL.

```sql
SELECT *
FROM likes
LEFT JOIN posts ON posts.id = likes.post_id
LEFT JOIN comments ON comments.id = likes.comment_id;


--- OUTPUT ---
   id   |         created_at         | user_id | post_id | comment_id |  id   |         created_at         |         updated_at         |              url              |caption                     | lat | lng  | user_id |  id   |         created_at         |         updated_at         |contents          | user_id | post_id 
--------+----------------------------+---------+---------+------------+-------+----------------------------+----------------------------+-------------------------------+----------------------------+-----+------+---------+-------+----------------------------+----------------------------+------------------+---------+---------
      1 | 2015-09-11 06:38:03.774+00 |    1930 |    1215 |            |  1215 | 2015-08-09 06:09:17.764+00 | 2013-05-09 03:53:11.489+00 | http://lorempixel.com/640/480 | quis qui aspernatur ...
      3 | 2016-05-24 03:06:32.237+00 |    2279 |    3842 |            |  3842 | 2016-05-10 14:58:33.855+00 | 2012-06-29 21:54:00.802+00 | http://lorempixel.com/640/480 | #javon #productize...
      4 | 2016-08-31 02:58:18.505+00 |    2146 |         |      28124 |       |                            |                            |                               |                            |     |      |         | 28124 | 2016-08-11 11:14:21.039+00 | 2016-08-11 11:14:21.039+00 | Tenetur iusto qui|    2839 |    5049
      7 | 2016-11-30 10:17:04.954+00 |    4662 |     348 |            |   348 | 2016-10-30 15:07:17.839+00 | 2013-03-23 17:59:40.966+00 | http://lorempixel.com/640/480 | accusantium expedit...
      8 | 2013-03-10 03:40:02.145+00 |     228 |   10932 |            | 10932 | 2013-02-25 01:24:31.287+00 | 2011-04-25 01:43:00.362+00 | http://lorempixel.com/640/480 | expedita architecto...
      9 | 2016-03-05 23:44:07.682+00 |    4126 |    4456 |            |  4456 | 2016-02-23 12:28:03.132+00 | 2012-08-23 14:26:58.5+00   | http://lorempixel.com/640/480 | id id laudantium #2...
      ...
      ...
 752001 | 2018-03-12 11:43:24.095+00 |    2501 |         |      59747 |       |                            |                            |                               |                            |     |      |         | 59747 | 2018-02-08 09:05:33.823+00 | 2018-02-08 09:05:33.823+00 | Dolores cum excep|    3715 |    9039
 752004 | 2013-10-05 03:19:51.869+00 |    2785 |         |      56067 |       |                            |                            |                               |                            |     |      |         | 56067 | 2013-09-13 13:04:43.824+00 | 2013-09-13 13:04:43.824+00 | Omnis consequatur|    5338 |   11277
 752006 | 2014-04-25 07:38:31.335+00 |    1401 |         |       5874 |       |                            |                            |                               |                            |     |      |         |  5874 | 2013-09-27 20:02:32.692+00 | 2013-09-27 20:02:32.692+00 | Rerum dignissimos|    1553 |    6838
 752007 | 2015-04-02 15:55:07.68+00  |    2380 |         |      20494 |       |                            |                            |                               |                            |     |      |         | 20494 | 2015-03-04 08:57:34.314+00 | 2015-03-04 08:57:34.314+00 | Consequatur rerum|    3959 |    8512
(752009 rows)
```

You may wonder why we would think of using LEFT JOIN? You can first try using the most common inner JOIN as follows:

```sql
SELECT *
FROM likes
JOIN posts ON posts.id = likes.post_id
JOIN comments ON comments.id = likes.comment_id;


--- OUTPUT ---
 id | created_at | user_id | post_id | comment_id | id | created_at | updated_at | url | caption | lat | lng | user_id | id | created_at | updated_at | contents | user_id | post_id 
----+------------+---------+---------+------------+----+------------+------------+-----+---------+-----+-----+---------+----+------------+------------+----------+---------+---------
(0 rows)
```

You will find that you get 0 rows, because a row can only like a post or a comment, not both at the same time, which leads to this result. But when you see this result, you should know that you cannot use inner JOIN, and should use LEFT JOIN instead.

Then, we need to find a way to group based on the created_at of posts and comments. Postgres provides us with the function `date_trunc` which can round down a timestamp to a specified time unit.

```sql
SELECT
  date_trunc('week', COALESCE(posts.created_at, comments.created_at)) AS week  -- round down to the nearest week
  --                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ select the first non-null value
FROM likes
LEFT JOIN posts ON posts.id = likes.post_id
LEFT JOIN comments ON comments.id = likes.comment_id
ORDER BY week;  -- order by the week



--- OUTPUT ---
          week          
------------------------
 2010-01-11 00:00:00+00  -- the very first date of posts.created_at or comments.created_at
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00  -- other dates not out of the week will be rounded down to the nearest week
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-11 00:00:00+00
 2010-01-18 00:00:00+00  -- pass a week, now nearest week set to 2010-01-18
 2010-01-18 00:00:00+00
 2010-01-18 00:00:00+00
 2010-01-18 00:00:00+00
 2010-01-18 00:00:00+00
 2010-01-18 00:00:00+00
 2010-01-18 00:00:00+00
...
...

 2019-12-23 00:00:00+00
 2019-12-23 00:00:00+00
 2019-12-23 00:00:00+00
(752009 rows)
```

Now, we can use `week` to group the data and count the number of likes in each week.

```sql
SELECT
  date_trunc('week', COALESCE(posts.created_at, comments.created_at)) AS week,
  COUNT(posts.id) AS num_likes_for_posts, -- count the number of likes for posts, COUNT() won't count NULL values, so it fit our needs
  COUNT(comments.id) AS num_likes_for_comments -- count the number of likes for comments
FROM likes
LEFT JOIN posts ON posts.id = likes.post_id
LEFT JOIN comments ON comments.id = likes.comment_id
GROUP BY week; -- group by the week



--- OUTPUT ---
          week          | num_likes_for_posts | num_likes_for_comments 
------------------------+---------------------+------------------------
 2017-05-22 00:00:00+00 |                 572 |                    646
 2014-02-10 00:00:00+00 |                1237 |                   1584
 2018-12-17 00:00:00+00 |                 316 |                    346
...
...
 2010-06-28 00:00:00+00 |                 150 |                     12
 2018-02-05 00:00:00+00 |                 666 |                    650
 2012-02-13 00:00:00+00 |                 609 |                    318
(518 rows)
```

However, we found that the entire query is very time-consuming (taking about 20 seconds), mainly due to the three-way joins (LEFT JOIN). We will address this issue in the next topic.



