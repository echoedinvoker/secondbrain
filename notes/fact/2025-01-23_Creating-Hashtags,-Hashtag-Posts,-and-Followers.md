---
date: 2025-01-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Creating Hashtags, Hashtag Posts, and Followers

## Hashtags

```js
Table hashtags {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  title VARCHAR(20)
}

Table hashtag_posts {
  id SERIAL [pk, increment]
  post_id INTEGER [ref: > posts.id]
  hashtag_id INTEGER [ref: > hashtags.id]
}
```

Let's convert the above design schemas into SQLs:

```sql
CREATE TABLE hashtags (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  title VARCHAR(20) NOT NULL UNIQUE
  --                ^^^^^^^^^^^^^^^
  --                title is what a hashtag is, and it should be unique
);
```
```sql
CREATE TABLE hashtag_posts (
  id SERIAL PRIMARY KEY,

  -- no need for created_at, updated_at,
  -- we've discussed this in the previous topics

  post_id INTEGER REFERENCES posts(id) ON DELETE CASCADE,
  hashtag_id INTEGER REFERENCES hashtags(id) ON DELETE CASCADE,

  -- although a post can hashtag the same title multiple times,
  -- it should be only stored once in the database
  UNIQUE(post_id, hashtag_id)
);
```


## Followers

Just associate the user with another user, and this relationship is unique.

```js
Table follower {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  leader_id INTEGER [ref: > users.id]
  follower_id INTEGER [ref: > users.id]
}
```

Let's convert the above design schema into SQL and add unique constraints:

```sql
CREATE TABLE followers (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  leader_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
  follower_id INTEGER REFERENCES users(id) ON DELETE CASCADE,

  -- a follower can only follow a leader once
  UNIQUE(leader_id, follower_id)
);
```



