---
date: 2025-01-11
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Really Awesome intevals

Date type `INTERVAL` means duration of time. It's somewhat similar to `DATE` or `TIME` type but it stores duration of time instead of date.

```sql
SELECT '1 Day 20 Hours 22 Minutes 7 Seconds'::INTERVAL;

--- OUTPUT ---
    interval    
----------------
 1 day 20:22:07
(1 row)
```

```sql
SELECT '1 D 20 H'::INTERVAL;

--- OUTPUT ---
    interval    
----------------
 1 day 20:00:00
(1 row)
```

Like `DATE` and `TIME`, If column is defined as `INTERVAL`, Postgres will try to convert string of any format to fixed interval value.

But what we said that `INTERVAL` is awesome, right? Let's see why.

```sql
SELECT '1 D 20 H'::INTERVAL - '1 Day'::INTERVAL;

 ?column? 
----------
 20:00:00
(1 row)
```
See? You can do arithmetic operations on `INTERVAL` values. That's why it's awesome.

Maybe you think that it isn't useful enough. But consider this example.

```sql
SELECT 'Nov 2024 18'::DATE - '1 Day'::INTERVAL;

      ?column?       
---------------------
 2024-11-17 00:00:00
(1 row)
```

Yes, you can subtract or add `INTERVAL` to `DATE`, `TIME` or `TIMESTAMP` values. That's why it's really awesome.

One last thing, the data types `DATE`, `TIME`, and `TIMESTAMP` can also be subtracted from each other, resulting in an `INTERVAL` value.

```sql
SELECT 'Nov 2024 18'::DATE - 'Nov 2024 1'::DATE;
--                  ^^^^^^               ^^^^^^ two DATE subtracted

--- OUTPUT ---
 ?column? 
----------
      17 -- this is INTERVAL type value
(1 row)
```

Let's give another even more impressive example.

```sql
SELECT '2025-1-1 18:00:00 EST'::TIMESTAMP - '2025-1-1 12:00:00 PST'::TIMESTAMP;
--                        ^^^                                  ^^^

--- OUTPUT ---
 ?column? 
----------
06:00:00 -- this is INTERVAL type value
(1 row)
```

Even two `TIMESTAMP` values with different timezones can be subtracted from each other, it also results correct `INTERVAL` value.

That means we don't need to use any external library or write any complex code to calculate duration of time between two dates or times in our web application or any other software. We can do it with simple SQL queries.


