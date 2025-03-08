---
date: 2025-01-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Including the Hashtag Table

After [[2025-01-21_Tables-for-Hashtags|this topic]], we already know how to create tables for hashtags of posts. Now, we want to include these tables in the schema.

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
  caption VARCHAR(240)
  lat REAL
  lng REAL
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

Table photo_tags {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  post_id INTEGER [ref: > posts.id]
  user_id INTEGER [ref: > users.id]
  x INTEGER
  y INTEGER
}

Table caption_tags {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  post_id INTEGER [ref: > posts.id]
  user_id INTEGER [ref: > users.id]
}

// create two more tables for hashtags of posts
Table hashtags {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  // hashtags just exist or not, no update needed
  title VARCHAR(20)
}

Table hashtag_posts {
  id SERIAL [pk, increment]
  // hashtags are created inside the post caption, so we can check post created_at time
  // to know when the hashtag was created
  post_id INTEGER [ref: > posts.id]
  hashtag_id INTEGER [ref: > hashtags.id]
}
```

![hashtags-to-schema.png](../assets/imgs/hashtags-to-schema.png)
