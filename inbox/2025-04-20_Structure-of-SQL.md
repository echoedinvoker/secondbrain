---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Structure of SQL

```sql
  CREATE TABLE cities (
--^^^^^^^^^^^^ ^^^^^^ identifier
--^^^^^^^^^^^^ keyword

    name VARCHAR(50),
--  ^^^^ ^^^^^^^ identifier
--  ^^^^ keyword
    country VARCHAR(50),
    population INTEGER,
    area INTEGER
  );

```
Usually, SQL keywords are written in uppercase, while identifiers are written in lowercase. This is not a strict rule, but it is a common convention to improve readability.


**keywords**:

Tell the database that we want to do something. Always written in uppercase.


**identifiers**:

Tell the database what we want to act on. Always written in lowercase.
