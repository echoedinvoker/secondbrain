---
date: 2025-02-06
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Automatically Generated Indexes

If a column is a primary key or has a unique constraint, PostgreSQL will automatically create an index for this column.

```sql
CREATE TABLE hashtags (
  id SERIAL PRIMARY KEY,
  --        ^^^^^^^^^^^ --> Automatically creates an index `hashtags_pkey` on `id`
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  title VARCHAR(255) UNIQUE
  --                 ^^^^^^ --> Automatically creates an index `hashtags_title_key` on `title`
);
```

Therefore, for columns with a primary key or unique constraint, we do not need to create an additional index.

For some database client such as pgAdmin, you won't see indexes created for primary key or unique constraint columns in the indexes section. However, you can query them from the `pg_class` table as below:

```sql
SELECT relname, relkind
FROM pg_class -- table that store all objects in the database
WHERE relkind = 'i'; -- i stands for index


--- OUTPUT ---
                    relname                     | relkind 
------------------------------------------------+---------
 pg_toast_16390_index                           | i
 users_pkey                                     | i
 comments_pkey                                  | i
 hashtags_pkey                                  | i  -- Automatically created index for primary key
 hashtags_title_key                             | i  -- Automatically created index for unique constraint
 posts_pkey                                     | i
 likes_pkey                                     | i
 pg_toast_1255_index                            | i
 pg_toast_1247_index                            | i
 pg_toast_2604_index                            | i

 --...
 --...


 ```
