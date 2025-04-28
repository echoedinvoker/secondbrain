---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 42: test to endpoint that create new todo

```py
# TodoApp/tests/test_todos.py
...

def test_create_todo():
#                   ^^ no need to use fixture this time
    request_data = {
        "title": "Test create todo",
        "description": "Test creating a todo",
        "priority": 3,
        "completed": False
    }
    response = client.post("/todos", json=request_data)
    assert response.status_code == status.HTTP_201_CREATED

```

We can further check if there is a new todo in the MOCK database in the same test:

```py
def test_create_todo_authenticated():
    request_data = {
        "title": "Test create todo",
        "description": "Test creating a todo",
        "priority": 3,
        "completed": False
    }
    response = client.post("/todos", json=request_data)
    assert response.status_code == status.HTTP_201_CREATED

    # further check if there is a new todo in the MOCK database
    db = TestingSessionLocal()
    todo = db.query(Todos).first() # because we didn't use fixture to insert any data this test,
                                   # so there should only be one todo we just created in the MOCK database
                                   # we can directly query the first todo to get it

    # assert the instance of the todo, and then assert each attribute of it
    assert isinstance(todo, Todos)
    assert todo.title == request_data["title"]
    assert todo.description == request_data["description"]
    assert todo.priority == request_data["priority"]
    assert todo.completed == request_data["completed"]

```

Let's run the test:

```sh
 pytest --disable-warnings -vv
==================================================================================================================== test session starts ====================================================================================================================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0 -- /home/matt/Projects/fastapi/fastapienv/bin/python
cachedir: .pytest_cache
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 6 items                                                                                                                                                                                                                                           

TodoApp/test/test_main.py::test_return_health_check PASSED                                                                                                                                                                                            [ 16%]
TodoApp/test/test_todos.py::test_read_all_authenticated PASSED                                                                                                                                                                                        [ 33%]
TodoApp/test/test_todos.py::test_read_one_authenticated PASSED                                                                                                                                                                                        [ 50%]
TodoApp/test/test_todos.py::test_read_one_authenticated_not_found PASSED                                                                                                                                                                              [ 66%]
TodoApp/test/test_todos.py::test_create_todo_authenticated PASSED                                                                                                                                                                                     [ 83%]
TodoApp/test/test_todos.py::test_cleanup PASSED                                                                                                                                                                                                       [100%]

=============================================================================================================== 6 passed, 1 warning in 0.49s ================================================================================================================

