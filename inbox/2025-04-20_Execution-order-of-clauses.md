---
date: 2025-04-20
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Execution order of clauses

SQL clauses are typically not executed from left to right.

```sql
SELECT name FROM cities WHERE area > 4000;
```
In the above example, the execution order of the clauses is as follows:

1. *FROM cities*: filter table from the database

2. *WHERE area > 4000*: filter rows from the table

3. *SELECT name*: filter columns from the rows
