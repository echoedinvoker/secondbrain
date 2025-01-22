---
date: 2025-01-01
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Each Join in Practice

In [[2025-01-01_Missing-Data-in-Joins|this topic]], we didn't archive the additional requirement of listing all rows of table `photos`.

But after learning about [[2025-01-01_Four-type-of-join|four type of join]], we can use the `LEFT JOIN` to achieve it.

```sql
SELECT
  url, username
FROM
  photos
LEFT JOIN  -- keep all rows of table photos
  users ON photos.user_id = users.id;


--- OUTPUT ---
          url           |      username       
------------------------+---------------------
 http://johnson.info    | Reyna.Marvin
 http://kolby.org       | Reyna.Marvin
 http://marjolaine.name | Reyna.Marvin
 https://elinore.name   | Micah.Cremin
 https://linda.info     | Micah.Cremin
 https://deon.biz       | Micah.Cremin
 http://colten.net      | Micah.Cremin
 http://jerrold.org     | Micah.Cremin
 http://sasha.com       | Alfredo66
 https://kailyn.name    | Alfredo66
 https://santina.net    | Alfredo66
 https://tyrique.info   | Gerard_Mitchell42
 http://dayne.com       | Gerard_Mitchell42
 http://isaias.net      | Gerard_Mitchell42
 https://meredith.net   | Gerard_Mitchell42
 http://buddy.info      | Frederique_Donnelly
 https://marina.com     | Frederique_Donnelly
 https://adelbert.biz   | Frederique_Donnelly
 http://chet.net        | Frederique_Donnelly
 https://alayna.net     | Frederique_Donnelly
 http://banner.jpg      | 
(21 rows)  -- list all rows of table photos even if there is no matching row in table users
```

For testing right outer join, we insert a new user to table users and do the right outer join.

```sql
INSERT INTO users (username) VALUES ('matt666');

SELECT
  url, username
FROM
  photos
RIGHT JOIN -- keep all rows of table users
  users ON photos.user_id = users.id;


--- OUTPUT ---
INSERT 0 1 -- insert a new user to table users successfully
          url           |      username       
------------------------+---------------------
 http://johnson.info    | Reyna.Marvin
 http://kolby.org       | Reyna.Marvin
 http://marjolaine.name | Reyna.Marvin
 https://elinore.name   | Micah.Cremin
 https://linda.info     | Micah.Cremin
 https://deon.biz       | Micah.Cremin
 http://colten.net      | Micah.Cremin
 http://jerrold.org     | Micah.Cremin
 http://sasha.com       | Alfredo66
 https://kailyn.name    | Alfredo66
 https://santina.net    | Alfredo66
 https://tyrique.info   | Gerard_Mitchell42
 http://dayne.com       | Gerard_Mitchell42
 http://isaias.net      | Gerard_Mitchell42
 https://meredith.net   | Gerard_Mitchell42
 http://buddy.info      | Frederique_Donnelly
 https://marina.com     | Frederique_Donnelly
 https://adelbert.biz   | Frederique_Donnelly
 http://chet.net        | Frederique_Donnelly
 https://alayna.net     | Frederique_Donnelly
                        | matt666
(21 rows)  -- list all rows of table users even if there is no matching row in table photos
```

Now, there are independent rows of both tables, so we can test the full outer join:

```sql
SELECT
  url, username
FROM
  photos
FULL JOIN -- keep all rows of both tables
  users ON photos.user_id = users.id;


--- OUTPUT ---
          url           |      username       
------------------------+---------------------
 http://johnson.info    | Reyna.Marvin
 http://kolby.org       | Reyna.Marvin
 http://marjolaine.name | Reyna.Marvin
 https://elinore.name   | Micah.Cremin
 https://linda.info     | Micah.Cremin
 https://deon.biz       | Micah.Cremin
 http://colten.net      | Micah.Cremin
 http://jerrold.org     | Micah.Cremin
 http://sasha.com       | Alfredo66
 https://kailyn.name    | Alfredo66
 https://santina.net    | Alfredo66
 https://tyrique.info   | Gerard_Mitchell42
 http://dayne.com       | Gerard_Mitchell42
 http://isaias.net      | Gerard_Mitchell42
 https://meredith.net   | Gerard_Mitchell42
 http://buddy.info      | Frederique_Donnelly
 https://marina.com     | Frederique_Donnelly
 https://adelbert.biz   | Frederique_Donnelly
 http://chet.net        | Frederique_Donnelly
 https://alayna.net     | Frederique_Donnelly
                        | matt666
 http://banner.jpg      | 
(22 rows) -- list all rows of both tables

```
