---
date: 2025-01-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Posts Creation

```js
Table posts {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  url VARCHAR(200)
  user_id INTEGER [ref: > users.id]
  caption VARCHAR(240)
  lat REAL
  lng REAL
}
```
Let's convert upper design schema of posts to SQL:

```sql
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  create_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  update_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  url VARCHAR(200) NOT NULL, -- in instagram, photo is the core of the post, so it's required
  caption VARCHAR(240), -- caption is optional

  -- lat and lng are optional, but if they are provided, they must be within the range
  lat REAL CHECK(lat IS NULL OR (lat >= -90 AND lat <= 90)),
  lng REAL CHECK(lng IS NULL OR (lng >= -180 AND lng <= 180)),
  --       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ using CHECK to implement range if not null

  -- instagram doesn't allow ophran posts, so user_id is required
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE
  --                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  --                       Tell psql that user_id is a foreign key that references users(id)
  --                       and if a user is deleted, all of their posts should be deleted as well
);
```

Let's discuss when we should do validation in the database level:

**Rules around validating the value might change frequently**
No validation in the database level.

**Rules around validating the value are complex**
No validation in the database level.

**We want to make sure we have the right type or domain of value**
Add validation!

