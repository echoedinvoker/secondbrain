---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 15: What is foreign key and how would we use it?

## What is a foreign key?
- A foreign key (FK) is a column within a relational database table that provides a link between two separate tables.
- A foreign key references a primary key in another table.
- Most relational databases need foreign keys to be able to link tables together to present data.


## In our case, foreign keys only exist in the `todos` table.
Because the `users` table and the `todos` table are one-to-many related, the `todos` table has a foreign key column called `owner` that references the `id` column in the `users` table.

So, we only find foreign keys in the `todos` table, not in the `users` table.


## Each API request, a user will have their ID attached to the request.
We will use JWT to authenticate users, JWT will provide a user's ID.

When a user makes a request, the JWT token will be sent along with the request. It means that the user's ID will be attached to the request. So, the server can use the user's ID to filter out the todos that belong to the user.
