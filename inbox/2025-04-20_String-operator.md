---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# String operator

| Operator | Description
|----------|--------------------------------------------------|
| `||`     | Concatenation                                    |
| `CONCAT()` | Concatenation                                    |
| `LENGTH()` | Length of string                                 |
| `UPPER()`  | Convert to uppercase                             |
| `LOWER()`  | Convert to lowercase                             |

## Examples

```sql
SELECT name || ', ' || country AS location FROM cities;
```
```sql
SELECT CONCAT(name, ', ', country) AS location FROM cities;
```
Above two queries are equivalent.

We can use multiple string operators nested together as below:
```sql
SELECT CONCAT(UPPER(name), ', ', UPPER(country)) AS location FROM cities;
```
It is the same as:
```sql
SELECT UPPER(CONCAT(name, ', ', country)) AS location FROM cities;
```

