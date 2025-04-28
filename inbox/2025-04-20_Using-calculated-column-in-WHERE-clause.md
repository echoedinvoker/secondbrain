---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Using calculated column in WHERE clause

```sql
SELECT
  name,
  population / area AS density -- calculated column `density`
FROM
  cities
WHERE
  density > 8000;
--^^^^^^^ using calculated column in WHERE clause
```

Above query will throw an error because of [[2025-04-20_Execution-order-of-clauses|Execution order of clauses]]
