---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Delete a record

```sql
DELETE FROM    -- be careful, it's `DELETE FROM` not `DELETE`, the latter is to delete a table instead of a record
  cities       -- it's a bit like `INSERT INTO` keyword that specifies the table but act on the record
WHERE
  name = 'Tokyo';

```

