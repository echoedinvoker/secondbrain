---
date: 2024-12-30
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# String operators

In [[2024-12-30_Calculated-column|this topic]], we introduced some math operators. In this topic, we will introduce some string operators.

![string-operators.png](../assets/imgs/string-operators.png)

```sql
SELECT name || ', ' || country AS location
FROM cities;


     location      
-------------------
 Tokyo, Japan
 Delhi, India
 Shanghai, China
 Sao Paulo, Brazil
(4 rows)
```

Or you can use the `CONCAT()` function to do the same thing:

```sql
SELECT CONCAT(name, ', ', country) AS location
FROM cities;

```

You can use multiple string operators in the same expression:

```sql
SELECT CONCAT(UPPER(name), ', ', UPPER(country)) AS location
FROM cities;


     location      
-------------------
 TOKYO, JAPAN
 DELHI, INDIA
 SHANGHAI, CHINA
 SAO PAULO, BRAZIL
(4 rows)
```

It is the same as:

```sql
SELECT UPPER(CONCAT(name, ', ', country)) AS location
FROM cities;

```
