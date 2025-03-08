---
date: 2025-01-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Adding Captions and Locations

In [[2025-01-20_Additional-Features-Around-Posts|this topic]], we discussed what additional features we want to add to our posts table, let's start with captions and locations, which are simple enough to implement just by adding columns.

```js
Table users {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  username VARCHAR(30)
}

Table posts {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  url VARCHAR(200)
  user_id INTEGER [ref: > users.id]

  // new columns for captions and locations
  caption VARCHAR(240)
  lat REAL
  lng REAL
  //  ^^^^ decimal data type, not accurate but enough for our use case
}

Table comments {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  contents VARCHAR(240)
  user_id INTEGER [ref: > users.id]
  post_id INTEGER [ref: > posts.id]
}

Table Likes {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  user_id INTEGER [ref: > users.id]
  post_id INTEGER [ref: > posts.id]
  comment_id INTEGER [ref: > comments.id]
}
```

