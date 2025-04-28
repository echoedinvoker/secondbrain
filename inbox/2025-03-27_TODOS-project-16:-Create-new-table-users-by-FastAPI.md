---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 16: Create new table users by FastAPI

We already have a table `todos`, now we want to add a table `users` using FastAPI.

Before creating the `users` table, because the previous SQLite database file was named `todos.db`, we are now going to change it to `todosapp.db` to avoid confusion.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── database.py # Change the database name to todosapp.db
    ├── __pycache__
    ├── __init__.py
    ├── main.py
    ├── models.py
    ├── todos.db # Remove database file because SQLAlchemy only creates database, not modify it
    └── routers
```

```py
# database.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base


SQLALCHEMY_DATABASE_URL = "sqlite:///./todosapp.db"
#                                      ^^^^^^^^^^^ Change the database name from todosdb to todosapp.db

engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```
Then, we create a new ORM model `User` in `models.py`:

```py
# models.py
from sqlalchemy import Boolean, Column, Integer, String
from database import Base


# Create a new table `users` by ORM class
class Users(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True)
    username = Column(String, unique=True)
    first_name = Column(String)
    last_name = Column(String)
    hashed_password = Column(String) # user input password will be hashed and store in the database
                                     # next time user login, the input password will also be hashed and compare with the hashed password in the database
                                     # so no one can see the original password on the server
    is_active = Column(Boolean, default=True)
    role = Column(String)

class Todos(Base):...

```

Finally, lauch the FastAPI server and the `todosapp.db` database will be created with two tables `todos` and `users` automatically.


