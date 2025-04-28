---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Insert multiple records

```sql
INSERT INTO cities (name, country, population, area)
VALUES
  ('Delhi', 'India', 28125000, 2240),
  --                                ^ we can use a comma to continue the list of values
  ('Shanghai', 'China', 22125000, 4015),
  ('Sao Paulo', 'Brazil', 20935000, 3043);

```

Columns and values order issue is as same as [[2025-04-20_Insert-single-record|insert single record]]
