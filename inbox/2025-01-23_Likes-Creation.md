---
date: 2025-01-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Likes Creation

```js
Table Likes {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  user_id INTEGER [ref: > users.id]
  post_id INTEGER [ref: > posts.id]
  comment_id INTEGER [ref: > comments.id]
}
```

Covert upper design schema to SQL and not forget to add the constraint we discussed in [[2025-01-18_Alternate-Polymorphic-Association-Design|this topic]]:

```sql
CREATE TABLE likes (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  -- A "like" is for either a post or a comment,
  -- so we cannot use NOT NULL on post_id and comment_id.
  -- We need to check this on a separate CHECK constraint.
  post_id INTEGER REFERENCES posts(id) ON DELETE CASCADE,
  --             ^
  comment_id INTEGER REFERENCES comments(id) ON DELETE CASCADE,
  --                ^ no NOT NULL

  -- independent CHECK constraint to implement `either`
  CHECK (
    COALESCE((post_id)::BOOLEAN::INTEGER, 0)
    +
    COALESCE((comment_id)::BOOLEAN::INTEGER, 0)
    = 1
  )
);
```


