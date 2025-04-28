---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Insert single record

```sql
INSERT INTO cities (name, country, population, area)
--                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
--                  We can change the order as we prefer
VALUES ('Tokyo', 'Japan', 38505000, 8223);
--      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
--      The order of the values must match the order of the fields

```
