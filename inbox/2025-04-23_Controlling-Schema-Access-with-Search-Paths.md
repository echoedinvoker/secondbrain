---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Controlling Schema Access with Search Paths

When we use objects in PostgreSQL without specifying a schema, PostgreSQL determines which schema to use based on the search path. We can use the following command to query the contents of the current search path.

```sh
$ psql -U postgres -d socialnetwork_test

socialnetwork_test=# SHOW search_path;
#    search_path   
# -----------------
#  "$user", public
# (1 row)
```

The meaning of *$user, public* is that PostgreSQL will first check if there is any schema name that is the same as the username logging into PostgreSQL. If there is, it will use that schema, otherwise it will use the public schema.

We can modify the content of search_path.

```sh
socialnetwork_test=# SET search_path TO test, public;
# SET
```

The current content of search_path is *test, public*, which means PostgreSQL will first check if the test schema exists. If it does, it will use the test schema; otherwise, it will use the public schema.

Let's use an SQL query without specifying a schema to see if we can retrieve data from the test schema.

```sh
socialnetwork_test=# SELECT * FROM users;
#  id |  username                  ^^^^^ no schema specified
# ----+-------------
#   1 | matt123
#   2 | joycelyn456
# (2 rows)
```

Successfully obtained the data from the test schema.

Now, let's recover the search_path to its original state.

```sh
socialnetwork_test=# SET search_path TO "$user", public;
# SET
socialnetwork_test=# SELECT * FROM users;
#  id |          created_at           |          updated_at           |   bio    | username 
# ----+-------------------------------+-------------------------------+----------+----------
#   1 | 2025-04-23 03:16:41.971203+00 | 2025-04-23 03:16:41.971203+00 | test bio | testuser
# (1 row)
```
