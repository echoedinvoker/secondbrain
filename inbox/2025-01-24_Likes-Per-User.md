---
date: 2025-01-24
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Likes Per User


**Question:**
Show each username and the number of likes they have given.

**Solution:**
```sql
SELECT username, COUNT(*) AS num_likes
FROM likes
JOIN users ON users.id = likes.user_id
GROUP BY username


--- OUTPUT ---
         username         | num_likes 
--------------------------+-----------
 Desmond.Armstrong42      |       152
 Cortez_Schiller          |       150
 Angela61                 |       160
 Meredith26               |       136
 Gail45                   |       140
 -- ...
 -- ...
 Marquis.Johnson52        |       145
 Elenora.Oberbrunner      |       128
 Mafalda.Hudson50         |       138
 Ashley_Bayer             |       150
 Maddison.Nicolas         |       149
(5345 rows)
```



