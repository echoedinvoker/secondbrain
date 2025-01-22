---
date: 2025-01-01
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Missing Data in Joins

In [[2025-01-01_Alternate-Forms-of-Syntax|this topic]], we've said that the order of tables in `JOIN` and `FROM` could result in different outcomes in some scenarios. We'll discuss this issue in detail in this topic.

Let's say we still want to get the url and username of all photos, but there is an additional requirement **that MUST to list all the photos**.

```sql
SELECT
  url, username
FROM
  photos
JOIN
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
(20 rows)

```
The above query does list all the photos, but what if we add a photo record with a user_id field as NULL?

```sql
INSERT INTO
  photos (url, user_id)
VALUES
  ('http://banner.jpg', NULL);  -- insert a photo with user_id as NULL, which means it's not associated with any user

SELECT
  url, username
FROM
  photos
JOIN
  users ON photos.user_id = users.id;


--- OUTPUT ---
INSERT 0 1  -- the photo record is inserted successfully

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
(20 rows)  -- but the number of rows is still 20, which implies that the photo with user_id as NULL is not listed, so the additional requirement is not satisfied

```
So, we found that photos not associated with any user are not listed in the result set. This is because the `JOIN` clause filters out the photos with `user_id` as NULL. Only the photos associated with users are listed in the imaginary merged table.

![filter-out-no-associated-row.png](../assets/imgs/filter-out-no-associated-row.png)

