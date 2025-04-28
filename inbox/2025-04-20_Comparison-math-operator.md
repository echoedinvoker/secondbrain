---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Comparison math operator

| Operator | Description
|----------|--------------------------------------------------|
| `=`        | Equal to                                         |
| `!=`      | Not equal to                                     |
| `<>`      | Not equal to                                     |
| `>`        | Greater than                                     |
| `>=`      | Greater than or equal to                         |
| `<`      | Less than                                        |
| `<=`      | Less than or equal to                            |
| `BETWEEN` | Between two values (inclusive)                  |
| `IN`      | Within a set of values                           |
| `NOT IN` | Not within a set of values                      |


## Examples

```sql
SELECT name, area FROM cities WHERE area = 8223;
--                                       ^ not like other languages using == or === for comparison `equal to`
--                                         in SQL we use single `=` for comparison `equal to`
```
```sql
SELECT name, area FROM cities WHERE area BETWEEN 2000 AND 4000;
```
```sql
SELECT name, area FROM cities WHERE name IN ('Delhi', 'Shanghai');
--                                       ^^^^^^^^^^^^^^^^^^^^^^^^ to check if the value is in the dataset
--                                                                items of list can be string or number
```
```sql
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai');
--                                       ^^^^^^ not in the dataset

