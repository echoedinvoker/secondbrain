---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Comparison Math Operators

In [[2024-12-30_Filter-rows-with-WHERE|this topic]], we learned how to filter rows using the `WHERE` clause and used the `>` operator in `WHERE` clause.

There are other comparison operators that can be used in the `WHERE` clause. Here are some of them:

![comparison-math-operators.png](../assets/imgs/comparison-math-operators.png)

```sql
SELECT name, area FROM cities WHERE area = 8223;
--                                       ^ not like other languages using == or === for comparison `equal to`
--                                         in SQL we use single `=` for comparison `equal to`


 name  | area 
-------+------
 Tokyo | 8223
(1 row)
```

```sql
SELECT name, area FROM cities WHERE area != 8223;
--                                       ^^ not equal


   name    | area 
-----------+------
 Delhi     | 2240
 Shanghai  | 4015
 Sao Paulo | 3043
(3 rows)
```

