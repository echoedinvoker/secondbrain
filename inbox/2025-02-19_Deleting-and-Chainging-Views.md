---
date: 2025-02-19
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Deleting and Chainging Views

This topic mainly discusses how to delete and modify views. Taking the view `recent_posts` from [[2025-02-19_When-to-Use-a-View?|the previous topic]] as an example, suppose we now want to change it to show the most recent 15 posts.

```sql
CREATE OR REPLACE VIEW recent_posts AS (
    SELECT *
    FROM posts
    ORDER BY created_at DESC
    LIMIT 15 -- change from 10 to 15
);


--- OUTPUT ---
CREATE VIEW -- in fact, it's a replacement because the view already exists
```

Above, we only need to rewrite the content of the View, and use `CREATE OR REPLACE VIEW` instead of `CREATE VIEW` to modify the content of the View.

Now, if we want to delete the view `recent_posts`, we can use the following SQL statement:

```sql
DROP VIEW recent_posts;


--- OUTPUT ---
DROP VIEW
```

