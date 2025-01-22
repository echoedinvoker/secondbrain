---
date: 2025-01-11
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Boolean Data Type

Data type `BOOLEAN` has three possible values:
- TRUE
- FALSE
- NULL (not TRUE and not FALSE, simply means unknown)

Special attention should be paid to BOOLEAN columns, as they can accept various different values and will automatically convert them into one of the three values mentioned above.

![values-to-boolean.png](../assets/imgs/values-to-boolean.png)

```sql
SELECT 'yes'::BOOLEAN;

--- OUTPUT ---
 bool 
------
t  -- TRUE (in PostgreSQL, TRUE is displayed as 't' in the output)
(1 row)
```

```sql
SELECT null::BOOLEAN;

--- OUTPUT ---
 bool 
------
      -- NULL (in PostgreSQL, NULL is displayed as an empty string in the output)
(1 row)
      ```
