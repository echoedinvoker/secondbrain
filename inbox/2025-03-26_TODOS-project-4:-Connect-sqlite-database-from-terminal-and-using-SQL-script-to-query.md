---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 4: Connect sqlite database from terminal and using SQL script to query

In [[2025-03-26_TODOS-project-3:-create-database-by-fastAPI|TODOS project 3: create database by fastAPI]], we have created a SQLite database file `todos.db` by FastAPI. In this topic, we will connect to the database from the terminal and use SQL script to query the data.

```sh
 sudo pacman -S sqlite # install sqlite, we can use it to connect to the database
 sqlite3 /home/matt/Projects/fastapi/TodoApp/todos.db # connect to the database and enter the sqlite shell
sqlite> .tables # list all tables in the database
todos
sqlite> .schema todos # show the schema of the table todos
CREATE TABLE todos (
        id INTEGER NOT NULL, 
        title VARCHAR, 
        description VARCHAR, 
        priority INTEGER, 
        completed BOOLEAN, 
        PRIMARY KEY (id)
);
CREATE INDEX ix_todos_id ON todos (id);
sqlite> SELECT * FROM todos; # we can use SQL script in sqlite shell

```
