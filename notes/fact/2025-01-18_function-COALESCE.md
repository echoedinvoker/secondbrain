---
date: 2025-01-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# function COALESCE
 

## Introduction

This function returns the first non-NULL value in the list of arguments. If all arguments are NULL, it returns NULL.

```sql
SELECT COALESCE(null, 5);
--                    ^ first non-NULL value

--- OUTPUT ---
 coalesce 
----------
        5
(1 row)
```
```sql
SELECT COALESCE(10, 5);
--              ^^ first non-NULL value

--- OUTPUT ---
 coalesce 
----------
       10
(1 row)
```
```sql
SELECT COALESCE(null, null);
--              ^^^^^^^^^^ all NULL

--- OUTPUT ---
 coalesce 
----------
          -- return NULL
(1 row)
```

## Real World Example

```sql
SELECT COALESCE((123)::BOOLEAN::INTEGER, 0);
--               123 --> true -->  1
--                                 ^ first non-NULL value

--- OUTPUT ---
 coalesce 
----------
        1
(1 row)
```

```sql
SELECT COALESCE((null)::BOOLEAN::INTEGER, 0);
--               null --> null --> null   ^ first non-NULL value
--                      ^^^^^^^^^^^^^^^ don't forget that BOOLEAN and INTEGER types allow NULL value

--- OUTPUT ---
 coalesce 
----------
        0
(1 row)
```
