---
date: 2025-01-03
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Gotcha with Count

```sql
SELECT * FROM photos;

--- OUTPUT ---
 id |          url           | user_id 
----+------------------------+---------
  1 | https://santina.net    |       3
  2 | https://alayna.net     |       5
  3 | https://kailyn.name    |       3
  4 | http://marjolaine.name |       1
  5 | http://chet.net        |       5
  6 | http://jerrold.org     |       2
  7 | https://meredith.net   |       4
  8 | http://isaias.net      |       4
  9 | http://dayne.com       |       4
 10 | http://colten.net      |       2
 11 | https://adelbert.biz   |       5
 12 | http://kolby.org       |       1
 13 | https://deon.biz       |       2
 14 | https://marina.com     |       5
 15 | http://johnson.info    |       1
 16 | https://linda.info     |       2
 17 | https://tyrique.info   |       4
 18 | http://buddy.info      |       5
 19 | https://elinore.name   |       2
 20 | http://sasha.com       |       3
 21 | http://banner.jpg      |          -- this row is missing user_id
(21 rows)

```

```sql
SELECT COUNT(user_id) FROM photos;
--           ^^^^^^^

--- OUTPUT ---
 count 
-------
    20  -- instead of 21, because COUNT doesn't count NULL values of given column
(1 row)

```

If you want to count all rows, including those with NULL values, use `COUNT(*)` instead.

```sql
SELECT COUNT(*) FROM photos;

 count 
-------
    21
(1 row)
```

Above rule is also the same with `GROUP BY` clause.
