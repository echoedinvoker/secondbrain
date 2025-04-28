---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Update a record

```sql
UPDATE cities -- 1. filter the table to update
SET population = 3950500 -- 3. filter the column to update with new value
WHERE name = 'Shanghai'; -- 2. filter the record
```
