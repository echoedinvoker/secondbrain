---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Creating and Accessing Schemas

Let's create a schema called `test` in the `socialnetwork_test` database and add a table, insert some data, and query the data to familiarize ourselves with the schema concept.

```sh
$ psql -U postgres -d socialnetwork_test

socialnetwork_test=# CREATE SCHEMA test; # syntax to create a schema is simple
# CREATE SCHEMA                          

socialnetwork_test=#
CREATE TABLE test.users (
#            ^^^^^ any Postgres object can be prefixed with a schema name
#                  if there is no schema prefix, the default schema is used (public)
id SERIAL PRIMARY KEY,
username VARCHAR
);
# CREATE TABLE

socialnetwork_test=#
INSERT INTO test.users (username)
#           ^^^^^
VALUES
    ('matt123'),
    ('joycelyn456');
# INSERT 0 2

socialnetwork_test=# SELECT * FROM test.users;
#                                  ^^^^^
#  id |  username   
# ----+-------------
#   1 | matt123
#   2 | joycelyn456
# (2 rows)

