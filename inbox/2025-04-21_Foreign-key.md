---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Foreign key

**Foreign key** is a column in a table that is pointing to the [[2025-04-21_Primary-key|primary key]] of another table or even to other records in the same table. It's used to relate two records in different tables or the same table.

Be noted that **foreign key** can be *null* and *not unique* and *changable* which is very different from **primary key**.

```sql
CREATE TABLE photos (
  id SERIAL PRIMARY KEY,
  url VARCHAR(200),
  user_id INTEGER REFERENCES users(id) -- foreign key (references users table's id column, primary key)
  --      ^^^^^^^ even users id column is SERIAL, you should use INTEGER here
);
```

With foreign key column, we can create some [[2025-04-21_Useful-Queries-across-tables-with-foreign-key|useful queries across tables with foreign key]]

Foreign key can help us to maintain the integrity of the data in the database.
[[2024-12-31_Foreign-Key-Constrains-Around-Insertion|Foreign Key Constrains Around Insertion]]
[[2025-04-21_Default-Foreign-Key-Constrains-Around-Deletion|Default Foreign Key Constrains Around Deletion]]
