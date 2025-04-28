---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Create table

```sql
  CREATE TABLE cities (
--^^^^^^^^^^^^ ^^^^^^ table name
--^^^^^^^^^^^^ command to create a table

    name VARCHAR(50),
    country VARCHAR(50),
    population INTEGER,
    area INTEGER
--  ^^^^ ^^^^^^^ data type of the column
--  ^^^^ column name
  );

```

