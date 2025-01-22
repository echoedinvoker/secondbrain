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
UPDATE      -- there is no SELECT or FROM clause because we don't read data, we update it
  cities    -- first execute the UPDATE statement to filter the table to update
SET                        -- SET clause is used to specify the columns and their new values, which is the last execution step
  population = 3950500
WHERE                -- WHERE clause still works the same way as in SELECT statements
  name = 'Shanghai'; -- second, filter the rows to update



UPDATE 1
```

When we use `UPDATE`, we need to provide a enough restrictive `WHERE` clause to ensure that only the records we want to update are affected, otherwise it is easy to update multiple records (we will learn fool-proof methods to avoid this issue in the future).

