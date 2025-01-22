---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Inserting data to table


## Inserting a single record

```sql
INSERT INTO cities (name, country, population, area)
--                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ We can change the order as we please, but we must ensure the correspondence of values.
VALUES ('Tokyo', 'Japan', 38505000, 8223);

```
Although the fields order can be changed freely, their order must correspond to the order of the values.

![insert-order-matter.png](../assets/imgs/insert-order-matter.png)




## Adding multiple records at once

```sql
INSERT INTO cities (name, country, population, area)
VALUES
  ('Delhi', 'India', 28125000, 2240),
  ('Shanghai', 'China', 22125000, 4015),
  ('Sao Paulo', 'Brazil', 20935000, 3043);

```
