---
date: 2025-01-24
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Highest User ID's Exercise

**Question:**
Select the 3 users with the highest user IDs from the users table.

**Answer:**
```sql
SELECT id, username
FROM users
-- the keys are ORDER BY and LIMIT
ORDER BY id DESC
LIMIT 3;


--- OUTPUT ---
  id  |      username       
------+---------------------
 5345 | Osborne14
 5344 | Desmond.Armstrong42
 5343 | Marshall_Rogahn3
(3 rows)
```
