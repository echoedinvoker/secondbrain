---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 3: create database by fastAPI

In [[2025-03-25_TODOS-project-1:-database-connection-with-ORM-SQLAlchemy|TODOS project 1: database connection with ORM SQLAlchemy]], we have created a engine and session to connect to the database. And in [[2025-03-25_TODOS-project-2:-Create-a-model-for-todos-table|TODOS project 2: Create a model for todos table]], we have created a ORM model for the todos table.

But the real SQLite database has not been created yet. In this project, we will use fastAPI to create a SQLite database. It must utilize the good engine we previously created and the ORM model to implement it.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── __init__.py
    ├── database.py # export engine
    ├── models.py # export Base
    └── main.py # new file, create fastAPI app and create database (todos.db)
```

```py
from fastapi import FastAPI
import models
from database import engine


app = FastAPI()

models.Base.metadata.create_all(bind=engine) # create database according to the ORM model
                                             # this will only work if the `todos.db` file does not exist
                                             # if we add new models or change the existing models, we need to delete the `todos.db` file and run the application again
                                             # to create the new database

```

```sh
uvicorn TodoApp.main:app --reload

 tree
.
├── fastapienv
└── TodoApp
    ├── __init__.py
    ├── database.py
    ├── models.py
    ├── main.py
    └── todos.db # new file, created by fastAPI
                 # this file is the SQLite database which contains the todos table
```
