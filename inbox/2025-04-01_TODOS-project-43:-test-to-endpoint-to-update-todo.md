---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 43: test to endpoint to update todo

Before we start writing the test, let's check the endpoint we are going to test.
```py
# TodoApp/routers/todos.py
@router.put("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def update_todo(user: user_dependency,
                      db: db_dependency,
                      new_todo: TodoRequest,
                      todo_id: int = Path(gt=0)):
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")

    todo = db.query(Todos).filter(Todos.id == todo_id)\
            .filter(Todos.owner_id == user.get("id")).first()

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    setattr(todo, "title", new_todo.title)
    setattr(todo, "description", new_todo.description)
    setattr(todo, "priority", new_todo.priority)
    setattr(todo, "completed", new_todo.completed)

    db.add(todo)
    db.commit()
```

We are going to test the `update_todo` endpoint. We will insert a todo by fixture and then update it by sending a request to the endpoint from the test client.

```py
def test_update_todo_authenticated(test_todo):
#                                  ^^^^^^^^^ insert a todo by fixture
    # Update todo
    request_data = {
        "title": "Test update todo",
        "description": "Test updating a todo",
        "priority": 2,
        "completed": True
    }
    response = client.put("/todos/1", json=request_data) # send a request to the endpoint to update the todo from the test client
    assert response.status_code == status.HTTP_204_NO_CONTENT # check if the response status code is 204

    # further check if the todo is updated by querying the database directly
    db = TestingSessionLocal()
    todo = db.query(Todos).first()
    assert isinstance(todo, Todos)
    assert todo.title == request_data["title"]
    assert todo.description == request_data["description"]
    assert todo.priority == request_data["priority"]
    assert todo.completed == request_data["completed"]
```

Besides the success case, we should also test the failure cases. For example, when the id is not found, the response status code should be 404.

```py
def test_update_todo_authenticated_not_found(test_todo):
    request_data = {
        "title": "Test update todo",
        "description": "Test updating a todo",
        "priority": 2,
        "completed": True
    }
    response = client.put("/todos/999", json=request_data)
    #                             ^^^ todo_id not existing

    # make sure the response fits the expected status code and message written in the code
    assert response.status_code == status.HTTP_404_NOT_FOUND
    assert response.json() == { "detail": "Todo not found" }
```

```sh
 pytest --disable-warnings -vv
=================================================================================================== test session starts ===================================================================================================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0 -- /home/matt/Projects/fastapi/fastapienv/bin/python
cachedir: .pytest_cache
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 8 items                                                                                                                                                                                                         

TodoApp/test/test_main.py::test_return_health_check PASSED                                                                                                                                                          [ 12%]
TodoApp/test/test_todos.py::test_read_all_authenticated PASSED                                                                                                                                                      [ 25%]
TodoApp/test/test_todos.py::test_read_one_authenticated PASSED                                                                                                                                                      [ 37%]
TodoApp/test/test_todos.py::test_read_one_authenticated_not_found PASSED                                                                                                                                            [ 50%]
TodoApp/test/test_todos.py::test_create_todo_authenticated PASSED                                                                                                                                                   [ 62%]
TodoApp/test/test_todos.py::test_update_todo_authenticated PASSED                                                                                                                                                   [ 75%]
TodoApp/test/test_todos.py::test_update_todo_authenticated_not_found PASSED                                                                                                                                         [ 87%]
TodoApp/test/test_todos.py::test_cleanup PASSED                                                                                                                                                                     [100%]

============================================================================================== 8 passed, 1 warning in 0.52s ===============================================================================================
```
