---
date: 2025-01-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Creating Tables with CHECK

Follow the steps in [[2025-01-22_Back-to-Postgres|this topic]], we want to create a database named `instagram` and create tables for it.

```sql
CREATE DATABASE instagram;
```
Then, check the table users we design in the schema design tool.

```js
Table users {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  username VARCHAR(30)
}
```

Convert to SQL:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  --                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  --                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  --                   we usually let the database handle the timestamp instead of inserting it manually
  username VARCHAR(30) NOT NULL,
  --                   ^^^^^^^^ this field is required
);
```

**NOT NULL**
A value must be provided (empty strings are values!)

**DEFAULT**
Provide a default value if an INSERT statement doesn't give one

**not to add any constraints after the column definition**
It doesn't matter if a value exists

Let's keep add more columns to the table:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  username VARCHAR(30) NOT NULL,

  bio VARCHAR(400), -- no matter if a value exists
  avatar VARCHAR(200), -- when user signs up, this isn't necessary

  -- we always need a varification, either phone or email/password
  phone VARCHAR(25), -- instead of NOT NULL here, we should use independent constraint to check either phone or email/password are provided
  email VARCHAR(40),
  password VARCHAR(50),

  status VARCHAR(15) -- only few options, we'll look back to this in the future

  -- use independent constraint to check phone or email/password are provided
  CHECK (COALESCE(phone, (
    -- if one of email or password is NULL, return NULL
    CASE
      WHEN email IS NULL THEN NULL
      WHEN password IS NULL THEN NULL
    END
  )) IS NOT NULL)
);
```




