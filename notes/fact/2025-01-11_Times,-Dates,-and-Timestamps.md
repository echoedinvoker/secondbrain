---
date: 2025-01-11
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Times, Dates, and Timestamps


## DATE

```sql
SELECT 'Nov-4-2024'::DATE;

--- OUTPUT
    date    
------------
 2024-11-04
(1 row)
```

```sql
SELECT 'Nov 4 2024'::DATE;

--- OUTPUT --- 
    date    
------------
 2024-11-04
(1 row)
```

```sql
SELECT 'Nov 4, 2024'::DATE;

--- OUTPUT ---
    date    
------------
 2024-11-04
(1 row)
```

No matter what date string format we can imagine, Postgres can convert it into the same date value.


## TIME, TIME WITHOUT TIME ZONE

`TIME` and `TIME WITHOUT TIME ZONE` are the same data type in Postgres.

```sql
SELECT '01:23 AM'::TIME;

--- OUTPUT ---
   time   
----------
 01:23:00
(1 row)
````

```sql
SELECT '1:23 am'::TIME;

   time   
----------
 01:23:00
(1 row)
```
Same as date, no matter what time string format we can imagine, Postgres can convert it into the same time value.

```sql
SELECT '17:30'::TIME;

--- OUTPUT ---
   time   
----------
 17:30:00
(1 row)
```
```sql
SELECT '5:30 PM'::TIME;
--      ^    ^^

--- OUTPUT ---
   time   
----------
 17:30:00
(1 row)
```

And Postgres can also recognize descriptions such as PM, AM, morning, afternoon, evening, early morning, etc., and convert them into 24-hour time as above shown.


## TIME WITH TIME ZONE

For any data type related to time or date, Postgres will try to convert all possible string formats as much as possible, so we won't provide too many examples below.

```sql
SELECT '5:30 PM EST'::TIME WITH TIME ZONE;
--                  ^^^^^^^^^^^^^^^^^^^^^ added time zone after time

--- OUTPUT ---
   timetz    
-------------
 17:30:00-05
--       ^^^ means 5 hours behind UTC
(1 row)
```

## TIMESTAMP, TIMESTAMP WITHOUT TIME ZONE

`TIMESTAMP` is like `DATE` but with `TIME`.

```sql
SELECT '2025-01-11 10:41:37'::TIMESTAMP;


--- OUTPUT ---
      timestamp      
---------------------
 2025-01-11 10:41:37
(1 row)
```

## TIMESTAMP WITH TIME ZONE

`TIMESTAMP WITH TIME ZONE` is like `DATE` but with `TIME WITH TIME ZONE`.

```sql
SELECT '2025-01-11 10:41:37 EST'::TIMESTAMP WITH TIME ZONE;

--- OUTPUT ---
      timestamptz       
------------------------
 2025-01-11 23:41:37+08
(1 row)

