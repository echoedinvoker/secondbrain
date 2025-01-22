---
date: 2025-01-06
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Subqueries in a Join Clause

```sql
SELECT first_name
FROM users
JOIN ( ) as o
--    ^  ^^^^ MUST have a alias, like subquery in FROM clause
--    subquery in JOIN clause, allow any shape of data,
--    but need to compatible with ON clause
ON o.user_id = users.id;
-- ^^^^^^^^^

```

subquery:

```sql
SELECT user_id
FROM orders
WHERE product_id = 3;

--- OUTPUT ---
 user_id 
---------
      34
      16
      45
      13
      41
(5 rows)
```

Insert subquery into JOIN clause of the main query:

```sql
SELECT first_name
FROM users
JOIN (
  -- insert subquery
  SELECT user_id
  FROM orders
  WHERE product_id = 3
) as o
--^^^^ alias for subquery (MUST)
ON o.user_id = users.id;
-- ^^^^^^^^^ suquery only need to be compatible with ON clause

```
