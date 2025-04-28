---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# establish a one-to-many relationship

A user can have many posts, users and posts are two related resources with *one-to-many* relationship.

We can *add a foreign key column* to the posts table that *references the primary key* of the users table to establish this relationship.

The key point is *where to add the foreign key column* in the resource table, usually on the *many* side, in this example it is the posts table.
 
```sql
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  title VARCHAR(200),
  content TEXT,
  user_id INTEGER REFERENCES users(id) -- add a foreign key column and refer to the users table's primary key
                                       -- to establish a one-to-many relationship

```
