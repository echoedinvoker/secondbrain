---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 31: Switch to connect PostgreSQL DB from SQLite by FastAPI app

In order to connect to a PostgreSQL database from a FastAPI app, we need to install the `psycopg2-binary` package. This package is a PostgreSQL adapter for Python.

```sh
pip install psycopg2-binary
```

Then we need to change the database URL in the `database.py` file. And modify some create engine settings to change the database from SQLite to PostgreSQL.

```py
# database.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base


# SQLALCHEMY_DATABASE_URL = "sqlite:///./todosapp.db"
SQLALCHEMY_DATABASE_URL = "postgresql://postgres:1234@localhost/todos"
#                         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this is posgresql url, format is:
#                                                                      postgresql://username:password@host:port/database_name

# engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
engine = create_engine(SQLALCHEMY_DATABASE_URL)
#                                              ^ connect_args={"check_same_thread": False} is only for SQLite, so we remove it

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()

```

Launch the FastAPI app and now the database has no any data, let's create a new admin user to test the connection.

```sh
curl -X 'POST' \
  'http://localhost:8000/auth/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "username": "matt",
  "email": "matt@mail.com",
  "first_name": "matt",
  "last_name": "chang",
  "password": "xxxxxxxx",
  "role": "admin"
}'
```
got the response with status code 201 and request body `null`.

Then, we use `psql` to check the database, to see if the user is created.

```sh
$ sudo -iu postgres
[postgres@arch ~]$ psql -d todos
todos=# SELECT * FROM users;
 id | username |     email     | first_name | last_name |                       hashed_password                        | is_active | role  
----+----------+---------------+------------+-----------+----------------------- ---------------------------------------+-----------+-------
  1 | matt     | matt@mail.com | matt       | chang     | $2b$12$ntubNbwDQpgg1sPDGyWULul./btgd0KrquvrnrbgkSJZJyf91WUg2 | t         | admin
(1 row)
```
