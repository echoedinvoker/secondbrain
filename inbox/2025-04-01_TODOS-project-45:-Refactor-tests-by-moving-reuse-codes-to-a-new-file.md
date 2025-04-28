---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 45: Refactor tests by moving reuse codes to a new file

There is a lot of reuse code in the test files. We need to refactor the tests by moving the reuse code to a new file.

```sh
 tree
.
├── fastapienv
├── testdb.db
└── TodoApp
    ├── __init__.py
    ├── alembic
    ├── alembic.ini
    ├── database.py
    ├── main.py
    ├── models.py
    ├── routers
    └── test
        ├── __init__.py
        ├── test_main.py # move the reuse code to a new file
        ├── test_todos.py # move the reuse code to a new file
        └── utils.py # New file, This file will contain the reuse code
```

```py
# utils.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.pool import StaticPool
from ..database import Base
from fastapi.testclient import TestClient
import pytest
from ..main import app
from ..models import Todos

# database url
SQLALCHEMY_DATABASE_URL = "sqlite:///./testdb.db"

# engine
engine = create_engine(
    SQLALCHEMY_DATABASE_URL,
    connect_args={"check_same_thread": False},
    poolclass=StaticPool
)

# test session class
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# create tables in mock database
Base.metadata.create_all(bind=engine)

# override dependencies
def override_get_db():
    db = TestingSessionLocal()
    try:
        yield db
    finally:
        db.close()

def override_get_current_user():
    return {"username": 'matt', "id": 1, "role": 'admin'}

# test client
client = TestClient(app)

# fixture
@pytest.fixture
def test_todo():
    todo = Todos(
        title="Learn to code!",
        description="I want to learn to code in Python",
        priority=5,
        completed=False,
        owner_id=1
    )
    db = TestingSessionLocal()
    db.add(todo)
    db.commit()
    yield todo
    db.delete(todo)
    db.commit()
    db.close()
```


```py
# test_todos.py
from ..routers.todos import get_db, get_current_user
from ..main import app
from starlette import status
from ..models import Todos
from .utils import * # import the reuse code from utils.py


# Override codes do not move to utils.py, just move override dependencies
app.dependency_overrides[get_db] = override_get_db
app.dependency_overrides[get_current_user] = override_get_current_user

# tests
def test_read_all_authenticated(test_todo):...

def test_read_one_authenticated(test_todo):...

...

```

Run the tests to make sure everything is working as before.

```sh
 pytest --disable-warnings -vv
=================================================================================================== test session starts ===================================================================================================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0 -- /home/matt/Projects/fastapi/fastapienv/bin/python
cachedir: .pytest_cache
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 10 items                                                                                                                                                                                                        

TodoApp/test/test_main.py::test_return_health_check PASSED                                                                                                                                                          [ 10%]
TodoApp/test/test_todos.py::test_read_all_authenticated PASSED                                                                                                                                                      [ 20%]
TodoApp/test/test_todos.py::test_read_one_authenticated PASSED                                                                                                                                                      [ 30%]
TodoApp/test/test_todos.py::test_read_one_authenticated_not_found PASSED                                                                                                                                            [ 40%]
TodoApp/test/test_todos.py::test_create_todo_authenticated PASSED                                                                                                                                                   [ 50%]
TodoApp/test/test_todos.py::test_update_todo_authenticated PASSED                                                                                                                                                   [ 60%]
TodoApp/test/test_todos.py::test_update_todo_authenticated_not_found PASSED                                                                                                                                         [ 70%]
TodoApp/test/test_todos.py::test_delete_todo_authenticated PASSED                                                                                                                                                   [ 80%]
TodoApp/test/test_todos.py::test_delete_todo_authenticated_not_found PASSED                                                                                                                                         [ 90%]
TodoApp/test/test_todos.py::test_cleanup PASSED                                                                                                                                                                     [100%]

============================================================================================== 10 passed, 1 warning in 0.58s ==============================================================================================
```

