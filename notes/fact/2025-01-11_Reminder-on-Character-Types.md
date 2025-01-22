---
date: 2025-01-11
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Reminder on Character Types

When we create the schema for our characters, we can define the string columns with following types:

![string-data-types-group.png](../assets/imgs/string-data-types-group.png)
```sql
SELECT 'matt'::CHAR(2);
--           ^^^^^^^^^ means fixed length of characters

--- OUTPUT ---
 bpchar 
--------
ma -- only 2 characters are stored, the rest are removed
(1 row)
```

```sql
SELECT 'matt'::CHAR(6);
--                  ^ if the length is more than the string, it will be padded with spaces

--- OUTPUT ---
 bpchar 
--------
 matt  
--   ^^ there are two spaces at the end
(1 row)
```

```sql
SELECT 'matt'::VARCHAR;
--           ^^^^^^^^^ means this columns can store anay length of characters

--- OUTPUT ---
 varchar 
---------
 matt
(1 row)
```

```sql
SELECT 'matt'::VARCHAR(2);
--                    ^^^ we can add a limit to the length of the string
--                        to avoid storing too much data accidentally

--- OUTPUT ---
 varchar 
---------
ma -- only 2 characters are stored, the rest are removed
(1 row)
```


```sql
SELECT 'matt'::VARCHAR(6);
--                     ^ if the length is more than the string, it will not be padded with spaces
--                       so it really just acts as a limit

--- OUTPUT ---
 varchar 
---------
matt -- no padding with spaces (different from CHAR)
(1 row)
```

```sql
SELECT 'matt'::TEXT;
--           ^^^^^^ exactly the same as VARCHAR without a limit, no performance difference

--- OUTPUT ---
 text 
------
 matt
(1 row)
```

> There is no performance difference between string types like there is with number data types, so it is only a difference in validation. We just need to use the one that best fits our needs.


```sql
