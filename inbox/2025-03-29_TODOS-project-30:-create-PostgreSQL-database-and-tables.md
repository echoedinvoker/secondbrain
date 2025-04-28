---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 30: create PostgreSQL database and tables

We used SQLite as our database before, but now we are going to switch to PostgreSQL.

Connect to the PostgreSQL database using the `psql` command line tool, create a new database called `todos`, and create two new tables `users` and `todos`.

```sh
 sudo -iu postgres # using sudo permission to switch to the postgres user, which is more secure than `su - postgres`
[postgres@arch ~]$ psql # connect to the PostgreSQL database and enter the PostgreSQL command line interface
postgres=# CREATE DATABASE todos;
CREATE DATABASE
postgres=# \l
                                                      List of databases
      Name       |  Owner   | Encoding | Locale Provider | Collate |  Ctype  
-----------------+----------+----------+-----------------+---------+---------
 postgres        | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 
 todos           | postgres | UTF8     | libc            | C.UTF-8 | C.UTF-8 
 ```

 Execute the following SQL commands to create the `users` and `todos` tables in the `todos` database.

```sh
postgres=# \c todos # enter the `todos` database
todos=# DROP TABLE IF EXISTS users;

CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username VARCHAR(45) NOT NULL,
  email VARCHAR(200),
  first_name varchar(45),
  last_name varchar(45),
  hashed_password varchar(200) NOT NULL,
  is_active boolean DEFAULT true,
  role varchar(45)
);

DROP TABLE IF EXISTS todos;

CREATE TABLE todos (
  id SERIAL PRIMARY KEY,
  title varchar(200) NOT NULL,
  description varchar(200),
  priority integer,
  complete boolean DEFAULT false,
  owner_id integer NOT NULL REFERENCES users(id) ON DELETE CASCADE
);

# NOTICE:  table "users" does not exist, skipping
# DROP TABLE
# CREATE TABLE
# NOTICE:  table "todos" does not exist, skipping
# DROP TABLE
# CREATE TABLE
```
