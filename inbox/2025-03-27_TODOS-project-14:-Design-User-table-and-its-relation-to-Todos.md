---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 14: Design User table and its relation to Todos

We need to create a new table `users` to store user data.

It will include the following fields:
- id: primary key, numeric
- email: string
- username: string
- first_name: string
- last_name: string
- hashed_password: string
- is_active: boolean

And the relation between `users` and `todos` should be one-to-many, which means that one user can have many todos, but one todo can only belong to one user.

In order to establish this relationship, we need to add a new field `owner` to the `todos` table that will be a foreign key to the primary key of the `users` table (so it should be `users.id`).

