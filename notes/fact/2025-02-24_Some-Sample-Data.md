---
date: 2025-02-24
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Some Sample Data

In order to demonstrate the functionality of the transaction, we create a table and insert two accounts into it.

```sql
CREATE TABLE accounts (
  id SERIAL PRIMARY KEY,
  name VARCHAR(20) NOT NULL,
  balance INTEGER NOT NULL
)


--- OUTPUT ---
CREATE TABLE
```

```sql
INSERT INTO accounts (name, balance)
VALUES
  ('Gia', 100),
  ('Alyson', 100);


--- OUTPUT ---
INSERT 0 2
```
