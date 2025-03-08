---
date: 2025-01-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Photo Tags and Caption Tags

```js
Table photo_tags {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  post_id INTEGER [ref: > posts.id]
  user_id INTEGER [ref: > users.id]
  x INTEGER
  y INTEGER
}
```
```js
Table caption_tags {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  post_id INTEGER [ref: > posts.id]
  user_id INTEGER [ref: > users.id]
}
```

Let's convert both design schemas above into SQLs:

```sql
CREATE TABLE photo_tags (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  post_id INTEGER NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  x INTEGER NOT NULL,
  y INTEGER NOT NULL,

  -- I think only one person can be tagged in a photo at a time.
  -- It doesn't seem very reasonable to tag the same person multiple times in one photo,
  -- so here we use UNIQUE to restrict this condition.
  UNIQUE(user_id, post_id)
);
```
We directly change the above SQL to creating caption_tags SQL:

```sql
CREATE TABLE photo_tags (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  -- caption_tags doesn't need updated_at column. We've discussed this before.
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  post_id INTEGER NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  -- caption_tags doesn't need x and y columns. We've discussed this before.

  -- Even one post can tag the same person multiple times in the caption.
  -- I think it should be only counted as one tag.
  -- So, this UNIQUE constraint is still necessary.
  UNIQUE(user_id, post_id)
);
```
