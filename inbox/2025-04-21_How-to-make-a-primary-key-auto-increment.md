---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# How to make a primary key auto increment

When creating a table in SQL, you can set the data type of the primary key to `SERIAL`, then it will automatically increment with each new row. Which also means you don't have to specify a value for the primary key when inserting new rows.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  -- ^^^^^^
  username VARCHAR(50)
);
```

```sql
INSERT INTO users (username) VALUES ('john_doe'); -- does not require id
