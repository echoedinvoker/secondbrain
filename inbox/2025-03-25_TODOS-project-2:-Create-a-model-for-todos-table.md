---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 2: Create a model for todos table

A *model* is an object used by SQLAlchemy to understand the structure of tables in a database.

When we define a class, we need to make it inherit from the `Base` object so that SQLAlchemy can recognize this class as a model and map it to a table in the database.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── database.py # export Base object
    ├── __init__.py
    └── models.py # new file to create a model for todos table
```

```py
# models.py
from sqlalchemy import Boolean, Column, Integer, String
from database import Base


class Todos(Base):
#           ^^^^ inherit from Base object, so that SQLAlchemy can recognize this class as a model
    __tablename__ = 'todos' # table name

    # define columns of the table
    id = Column(Integer, primary_key=True, index=True)
    #    ^^^^^^ ^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ because it's a primary key, index need to be set separately
    #    ^^^^^^ ^^^^^^^ when defining the type of the column, we need to use the classes from SQLAlchemy instead of Python's built-in types
    #    ^^^^^^ must use `Column` object to define a column, so that SQLAlchemy can understand this is a column of the table
    title = Column(String)
    description = Column(String)
    priority = Column(Integer)
    completed = Column(Boolean, default=False)
    #                           ^^^^^^^^^^^^^ default value for the column

```
