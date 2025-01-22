---
date: 2024-12-31
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Foreign Key Constrains Around Insertion

In [[2024-12-31_Generate-foreign-key-column|this topic]], we talked about how database ensure that related tables are data consistent: if we insert a row in a table that has a foreign key, the database will check that the value of the foreign key is actually pointing to a **EXISTING** row in the referenced table, or it will throw an error.

But, there are the third option: **SET NULL** to the foreign key value, let's see how it works.

```sql
INSERT INTO photos (url, user_id)
VALUES ('http://img0.jpg', NULL);
--                         ^^^^ in SQL, NULL is a special value that represents the absence of a value

--- Output ---
INSERT 0 1  -- PostgreSQL allows the insertion

```

So, the database allows that the foreign key column to be NULL, which means that the row is not related to any row in the referenced table.

The photo may be a default photo or a photo for homepage, so it's not necessary to have a user_id in this case.

Finally, we can conclude that the foreign key constrains around insertion can be:

![foreign-key-constrains.png](../assets/imgs/foreign-key-constrains.png)
