---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 44: test to endpoint to delete todo

Before we test the endpoint to delete a todo, let's check the implementation of the endpoint.

```py
# TodoApp/routers/todos.py
@router.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(user: user_dependency, db: db_dependency, todo_id: int = Path(gt=0)):

    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")
    
    query = db.query(Todos).filter(Todos.id == todo_id)\
            .filter(Todos.owner_id == user.get("id"))

    todo = query.first()

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    query.delete()
    db.commit()
```

Then, we can write the test to delete the todo inserted by the fixture.

```py
# TodoApp/tests/test_todos.py
def test_delete_todo_authenticated(test_todo):
#                                  ^^^^^^^^^ insert a todo by the fixture
    response = client.delete("/todos/1")
    #                                ^ delete the todo with id 1 (inserted by the fixture)
    assert response.status_code == status.HTTP_204_NO_CONTENT

    # further check if the todo with id 1 is deleted
    db = TestingSessionLocal()
    todo = db.query(Todos).filter(Todos.id == 1).first()
    #                      ^^^^^^^^^^^^^^^^^^^^^ 
    assert todo is None 
```

We need to consider some edge cases, such as deleting a todo that does not exist.

```py
def test_delete_todo_authenticated_not_found(test_todo):
    response = client.delete("/todos/999")
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
collected 9 items                                                                                                                                                                                                         

TodoApp/test/test_main.py::test_return_health_check PASSED                                                                                                                                                          [ 11%]
TodoApp/test/test_todos.py::test_read_all_authenticated PASSED                                                                                                                                                      [ 22%]
TodoApp/test/test_todos.py::test_read_one_authenticated PASSED                                                                                                                                                      [ 33%]
TodoApp/test/test_todos.py::test_read_one_authenticated_not_found PASSED                                                                                                                                            [ 44%]
TodoApp/test/test_todos.py::test_create_todo_authenticated PASSED                                                                                                                                                   [ 55%]
TodoApp/test/test_todos.py::test_update_todo_authenticated PASSED                                                                                                                                                   [ 66%]
TodoApp/test/test_todos.py::test_update_todo_authenticated_not_found PASSED                                                                                                                                         [ 77%]
TodoApp/test/test_todos.py::test_delete_todo_authenticated PASSED                                                                                                                                                   [ 88%]
TodoApp/test/test_todos.py::test_cleanup PASSED                                                                                                                                                                     [100%]

============================================================================================== 9 passed, 1 warning in 0.55s ===============================================================================================

```
