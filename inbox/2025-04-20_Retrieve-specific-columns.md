---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Retrieve specific columns

```sql
SELECT area, name, name FROM cities;
--     ^^^^^^^^^^^^^^^^      ^^^^^^ table which we want to retrieve data from
--     ^^^^^^^^^^^^^^^^      
--     we can specify the columns we want to retrieve with the order we want
--     or even repeat the same column multiple times (sometimes it's quite useful)
```

