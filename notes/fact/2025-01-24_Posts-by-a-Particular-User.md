---
date: 2025-01-24
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Posts by a Particular User


**Question:**
Join the users and posts table. Show the username of user ID 200 and the captions of all posts they have created.


**Solution:**
```sql
SELECT username, caption
FROM users
JOIN posts ON posts.user_id = users.id
WHERE users.id = 200;


--- OUTPUT ---
  username  |                                                     caption                                                     
------------+-----------------------------------------------------------------------------------------------------------------
 Casandra60 | #benchmark #cristina @Lauriane.Parker19
 Casandra60 | #rerum @Margarete.Rau
 Casandra60 | eum autem et #citlalli #anabelle #whiteboard #out-of-the-box #dolore #mindshare @Domingo_Stroman @Pascale.Bauch
(3 rows)

```


