---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Create Table

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

## Keywords and Identifiers

**keywords**:

Tell the database that we want to do something. Always written in uppercase.


**identifiers**:

Tell the database what we want to act on. Always written in lowercase.


## Column Data Types

**VARCHAR(50)**:

Variable length character string. Maximum length of 50 characters.

If we put a string longer than 50 characters, we'll get an error.


**INTEGER**:

Number without decimal places.

-2147483648 to 2147483647

Anything larger or smaller will give an error.
