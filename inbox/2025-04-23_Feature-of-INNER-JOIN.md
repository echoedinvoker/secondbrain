---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Feature of INNER JOIN

When we use `JOIN`, we are actually using `INNER JOIN`. The characteristic of `INNER JOIN` is that the joined columns must have values in both tables, or the result will not include those rows.


```sql
SELECT
  url, username
FROM
  photos
JOIN
  users ON photos.user_id = users.id;
  --              ^^^^^^^         ^^ both columns must have values,
  --                                 or the record will not be included into the joined table
```

So if insert a record into the `photos` table with a `NULL` value for `user_id`.

```sql
INSERT INTO
  photos (url, user_id)
VALUES
  ('http://banner.jpg', NULL);
```

Query the joined table again, you won't see the inserted record above.
