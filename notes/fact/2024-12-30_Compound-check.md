---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Compound check


## BETWEEN, IN, and NOT IN

In [[2024-12-30_Comparison-Math-Operators|this topic]], we have used some easy comparison operators. Now, we will learn the last three comparison operators: `BETWEEN`, `IN`, and `NOT IN`.

```sql
SELECT name, area FROM cities WHERE area BETWEEN 2000 AND 4000;

   name    | area 
-----------+------
 Delhi     | 2240
 Sao Paulo | 3043
(2 rows)
```

```sql
SELECT name, area FROM cities WHERE name IN ('Delhi', 'Shanghai');
--                                       ^^^^^^^^^^^^^^^^^^^^^^^^ to check if the value is in the list
--                                                                items of list can be string or number


   name   | area 
----------+------
 Delhi    | 2240
 Shanghai | 4015
(2 rows)
```

```sql
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai');
--                                       ^^^^^^ not in the list


   name    | area 
-----------+------
 Tokyo     | 8223
 Sao Paulo | 3043
(2 rows)
```


## Compound check

In the first query, we have used `AND` to support the `BETWEEN` operator. But it can also be used to combine multiple WHERE clauses as compound check.

```sql
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai') AND area > 4000;
--                                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^ ^^^^^^^^^^^ there are two clauses combined using AND


 name  | area 
-------+------
 Tokyo | 8223
(1 row)
```

```sql
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai') OR name = 'Delhi';
--                                                                    ^^ also combine two clauses but using OR


   name    | area 
-----------+------
 Tokyo     | 8223
 Delhi     | 2240
 Sao Paulo | 3043
(3 rows)
```
