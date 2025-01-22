---
date: 2025-01-01
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Testing Deletion Constrains

In [[2024-12-31_Constrains-Around-Deletion|this topic]], we discussed the constraints around deletion. Now, let's test them out.

For testing, we will re-run the similar queries below several times to see how the deletion constraints work:

```sql
DROP TABLE photos;

CREATE TABLE photos (
id SERIAL PRIMARY KEY,
url VARCHAR(200),
user_id INTEGER REFERENCES users(id) ON DELETE CASCADE
--                                   ^^^^^^^^^^^^^^^^^ add deletion constraint after foreign key definition
);
 
INSERT INTO photos (url, user_id)
VALUES
('http:/one.jpg', 4),
('http:/two.jpg', 1),
('http:/25.jpg', 1),
('http:/36.jpg', 1),
('http:/754.jpg', 2),
('http:/35.jpg', 3),
('http:/256.jpg', 4);


--- OUTPUT ---
DROP TABLE
CREATE TABLE
INSERT 0 7

```


For `ON DELETE CASCADE`, if we delete the referenced row in the parent table, the corresponding rows in the child table will also be deleted, let's test it:

```sql
DELETE FROM users WHERE id = 1;

SELECT * FROM photos;


--- OUTPUT ---
 id |      url      | user_id 
----+---------------+---------
  1 | http:/one.jpg |       4
  5 | http:/754.jpg |       2
  6 | http:/35.jpg  |       3
  7 | http:/256.jpg |       4
(4 rows)

-- The rows with user_id 1 are deleted from the photos table.

```

```sql
DROP TABLE photos;

CREATE TABLE photos (
id SERIAL PRIMARY KEY,
url VARCHAR(200),
user_id INTEGER REFERENCES users(id) ON DELETE SET NULL
--                                   ^^^^^^^^^^^^^^^^^^ add deletion constraint after foreign key definition
);
 
INSERT INTO photos (url, user_id)
VALUES
('http:/one.jpg', 4),
('http:/754.jpg', 2),
('http:/35.jpg', 3),
('http:/256.jpg', 4);

```

For `ON DELETE SET NULL`, if we delete the referenced row in the parent table, the corresponding rows in the child table will have their foreign key columns set to NULL, let's test it:

```sql
DELETE FROM users WHERE id = 4;

SELECT * FROM photos;

 id |      url      | user_id 
----+---------------+---------
  2 | http:/754.jpg |       2
  3 | http:/35.jpg  |       3
  1 | http:/one.jpg |        
  4 | http:/256.jpg |        
(4 rows)

-- The rows with user_id 4 are set to NULL in the photos table.

```
