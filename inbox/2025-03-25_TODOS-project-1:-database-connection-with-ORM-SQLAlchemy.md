---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 1: database connection with ORM SQLAlchemy

```sh
 tree
.
├── fastapienv # python virtual environment
└── TodoApp # project folder
    ├── database.py # codes for database connection
    └── __init__.py
```

```py
# database.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base


SQLALCHEMY_DATABASE_URL = "sqlite:///./todos.db" # where the database will be stored

engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}) # engine to connect to the database
#                                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                                               By default SQLite can only be used with a single thread.
#                                               But FastAPI uses multiple threads, so we need to set this argument to True.

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine) # when initialized, it will create a new session to interact with the database with the engine we created

Base = declarative_base() # base class for all ORM models, which will map to the database tables directly

```
