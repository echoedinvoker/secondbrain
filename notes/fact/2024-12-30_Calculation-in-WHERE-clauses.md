---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Calculation in WHERE clauses

In [[2024-12-30_Calculated-column|this topic]], we learned how to create a new column that is calculated based on existing columns. Can we use the new column in the WHERE clause? Let's find out.

```sql
SELECT
  name,
  population / area AS density  -- Create a new column by calculating the population density
FROM
  cities
WHERE
  density > 8000;
--^^^^^^^ use the new column in the WHERE clause

psql:/tmp/nvim.mattc/Q7QjFc/course-query-2024-12-30-14-48-32:7: ERROR:  column "density" does not exist
LINE 7:   density > 8000;
          ^

```

We got an error because the WHERE clause is executed before the SELECT clause. So it said that the column `density` does not exist. We have learned the execution order of SQL clauses in [[2024-12-30_Filter-rows-with-WHERE|this topic]].

But we can use the calculation directly in the WHERE clause to fix above query:

```sql
SELECT
  name,
  population / area AS density
FROM
  cities
WHERE
   population / area > 8000;
-- ^^^^^^^^^^^^^^^^^


 name  | density 
-------+---------
 Delhi |   12555
(1 row)
```

Now it works as expected. And be noted that the mathematical operators like `+`, `-`, `*`, `/` always have higher precedence than the comparison operators like `=`, `>`, `<`, `>=`, `<=`, `<>`.
