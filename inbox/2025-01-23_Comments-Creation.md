---
date: 2025-01-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Comments Creation

```js
Table comments {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  contents VARCHAR(240)
  user_id INTEGER [ref: > users.id]
  post_id INTEGER [ref: > posts.id]
}
```

Let's convert upper design schema of comments to SQL:

```sql
CREATE TABLE comments (
  id SERIAL PRIMARY KEY,
  create_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  update_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  contents VARCHAR(240) NOT NULL, -- contents are the comment itself, so it's required

  -- comments should be always associated with a user and a post
  -- whenever one of them is deleted, the comment should be deleted as well
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  post_id INTEGER NOT NULL REFERENCES posts(id) ON DELETE CASCADE
);
```


