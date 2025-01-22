---
date: 2025-01-11
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# More on Number Data Types

## Numbers without any decimal points

![integer-groups-type.png](../assets/imgs/integer-groups-type.png)

**if number value outside the range of the integer data type**

```sql
SELECT 99999::SMALLINT;
--          ^^^^^^^^^^ we can use ::[DATA_TYPE] to specify the data type


--- OUTPUT --- when value outside the range of the data type, PostgreSQL will throw an error
psql:/tmp/nvim.mattc/dau09J/postgres-query-2025-01-11-09-30-57:1: ERROR:  smallint out of range

```

**if number value has decimal points**

```sql
SELECT 2.3::SMALLINT;


-- OUTPUT --- no error, just remove the decimal points silently
 int2 
------
    2
(1 row)

```

## Decimal Data Type

![decimal-type-group.png](../assets/imgs/decimal-type-group.png)

```sql
SELECT 1.99999::REAL - 1.99998::REAL;

--- OUTPUT ---
   ?column?   
--------------
1.001358e-05  -- 1.99999 - 1.99998 = 0.00001001358
(1 row)       --                           ^^^^^^^ not accurate, because of the data type used REAL

```

```sql
SELECT 1.99999::DECIMAL - 1.99998::DECIMAL;


--- OUTPUT ---
 ?column? 
----------
  0.00001  -- 1.99999 - 1.99998 = 0.00001  -- accurate but less efficient
(1 row)

```
