---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Retrieve data from table


## Retrieve all columns from table

```sql
SELECT * FROM cities;
--     ^ shortcut for all columns of the table

```


   name    | country | population | area 
-----------+---------+------------+------
 Tokyo     | Japan   |   38505000 | 8223
 Delhi     | India   |   28125000 | 2240
 Shanghai  | China   |   22125000 | 4015
 Sao Paulo | Brazil  |   20935000 | 3043


## Retrieve specific columns from table

```sql
SELECT area, name, name FROM cities;
--     ^^^^^^^^^^^^^^^^ we can specify the columns we want to retrieve with the order we want
--                      or even repeat the same column multiple times (sometimes it's quite useful)
```
 area |   name    |   name    
------+-----------+-----------
 8223 | Tokyo     | Tokyo
 2240 | Delhi     | Delhi
 4015 | Shanghai  | Shanghai
 3043 | Sao Paulo | Sao Paulo
(4 rows)
