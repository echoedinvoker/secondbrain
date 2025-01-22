---
date: 2025-01-05
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Example of a Subquery in a From

In [[2025-01-05_Subqueries-in-a-FROM|subqueries in a FROM]] and [[2025-01-05_From-Subqueries-that-Return-a-Value|FROM Subqueries that Return a Value]], we have already discussed the usage of subquery in the FROM clause, but we used meaningless queries for demonstration.

This time, we will use a real example to illustrate the usage of subquery in the FROM clause.

![require-from-subquery.png](../assets/imgs/require-from-subquery.png)


My thinking logic starts from the result and works backwards, because the result we want is the average value, so the AVG() function should be used in the SELECT clause of the outer query:

```sql
SELECT AVG()
--     ^^^
FROM ( );


```

Next, because it is an average count, the SELECT clause in the subquery should be the COUNT(*) function. The requirement is to calculate the average count for each user, so it should be GROUP BY user_id.

subquery:

```sql
SELECT COUNT(*)
FROM orders
GROUP BY user_id;

--- OUTPUT ---
 count 
-------
    12
    11
    11
-- ...
    13
    12
     7
     9
(50 rows)

```

Finally, put the subquery into the FROM clause, and put the subquery result column 'count' into the AVG() function.

```sql
SELECT AVG(count)
--         ^^^^^
FROM (
  -- insert subquery to FROM clause
  SELECT COUNT(*)
  FROM orders
  GROUP BY user_id
) as p; -- don't forget to give the subquery an alias


--- OUTPUT ---
         avg         
---------------------
 11.0000000000000000
(1 row)

```
Then, we got the average count of orders per user.

There are many ways to solve problems, this is just one of them. The purpose of the above queries is to demonstrate how to use a subquery to solve real-world problems.


