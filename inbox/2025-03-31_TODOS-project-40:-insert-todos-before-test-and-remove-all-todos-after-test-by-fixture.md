---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 40: insert todos before test and remove all todos after test by fixture

In [[2025-03-31_TODOS-project-39:-Test-to-endpoint-that-read-all-todos|TODOS project 39: Test to endpoint that read all todos]], we created a test that reads all todos but there is no any todo in the MOCK database.

We want to insert some todos before the test and remove all todos after the test because all tests should be independent of each other. We can use the fixture to implement this.

```py
# TodoApp/tests/test_read_all_todos.py
from sqlalchemy import create_engine, text
# ...                                 ^^^^ use SQL script
import pytest # to use fixture
from ..models import Todos # to instantiate a todo

...

# create a fixture to insert todos before the test and remove all todos after the test
@pytest.fixture
def test_todo():
    # instantiate a todo
    todo = Todos(
        title="Learn to code!",
        description="I want to learn to code in Python",
        priority=5,
        completed=False,
        owner_id=1
    )
    # create a session and add the todo into the MOCK database
    db = TestingSessionLocal()
    db.add(todo)
    db.commit()

    yield todo # fixture will execute the codes before yield at the beginning of the test and after yield at the end of the test

    # remove all todos after the test
    with engine.connect() as con:
        con.execute(text("DELETE FROM todos;"))
        con.commit()

def test_read_all_authenticated(test_todo):
    response = client.get("/todos")
    assert response.status_code == status.HTTP_200_OK
    assert response.json() == [
        # .json() will get the response in JSON format
        # so we need to write the expected response in JSON format
        {
            "id": 1,
            "title": "Learn to code!",
            "description": "I want to learn to code in Python",
            "priority": 5,
            "completed": False,
            "owner_id": 1
        }
    ]
```

Let's run the test:

```sh
 pytest --disable-warnings
===================================== test session starts =====================================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 2 items                                                                             

TodoApp/test/test_main.py .                                                             [ 50%]
TodoApp/test/test_todos.py .                                                            [100%]

================================ 2 passed, 1 warning in 0.46s =================================

```

It's successful. But I'm curious why lecturer used `with engine.connect() as con:` to delete all todos instead of `db` object. Let's try to use `db` object to delete all todos and close the session btw.

```py
...

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
    
    # instead of using engine.connect(), try to use db object to delete all todos and close the session
    db.query(Todos).delete() # query all todos and delete them
    db.commit()

...

```

Run the test again:

```sh
 pytest --disable-warnings
===================================== test session starts =====================================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 2 items                                                                             

TodoApp/test/test_main.py .                                                             [ 50%]
TodoApp/test/test_todos.py .                                                            [100%]

================================ 2 passed, 1 warning in 0.44s =================================
```

Still successful. I've asked the lecturer about this. Let's see what he will say.


## Lecturer response about why he used `engine.connect()` to delete all todos

**Why not use todo.delete()?**

The DELETE FROM todos command is used to clear the entire table quickly and efficiently, which is ideal in a testing environment. Using db.delete(todo) would require loading each item into memory and deleting them individually, which is less efficient when resetting the database for tests.

**Purpose of the text function:**

The text function in SQLAlchemy allows the execution of raw SQL statements. In this case, it enables the direct execution of the DELETE statement, which is faster and more straightforward for bulk operations like clearing a table.


In general, the raw SQL approach is chosen for efficiency and simplicity when resetting the database during tests.
