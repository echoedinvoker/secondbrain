---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 41: test to endpoint that read specific todo by id

This test should be very similar to the previous test [[2025-03-31_TODOS-project-40:-insert-todos-before-test-and-remove-all-todos-after-test-by-fixture|TODOS project 40: insert todos before test and remove all todos after test by fixture]], and I've implemented the fixture, mock database and user, we just need to write the test itself now.

Before we start writing the test, let's check the endpoint that we are going to test.

```py
# TodoApp/routers/todos.py

...

@router.get("/todos/{todo_id}")
async def read_todo(user: user_dependency, db: db_dependency, todo_id: int):
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")

    todo = db.query(Todos).filter(Todos.id == todo_id)\
            .filter(Todos.owner_id == user.get("id")).first()

    if todo is not None:
        return todo

    raise HTTPException(status_code=404, detail="Todo not found")

...

```

We can use test client to test the endpoint just like we did in the previous test, let's write the test.

```py
...

def test_read_one_authenticated(test_todo):
    response = client.get("/todos/1")
    #                             ^ this id is exist in the database
    assert response.status_code == status.HTTP_200_OK
    assert response.json() == {
    #                         ^ this endpoint use `.first()`, so it will return the object instead of list
    #                         ^ this is different from the previous test (test_read_all_authenticated), be careful
        "id": 1,
        "title": "Learn to code!",
        "description": "I want to learn to code in Python",
        "priority": 5,
        "completed": False,
        "owner_id": 1
    }

# Also, we need to add the other test to check if the todo is not found.
def test_read_one_authenticated_not_found(test_todo):
    response = client.get("/todos/999")
    #                             ^^^ this id is not exist in the database

    # status code and response body should be the same as the endpoint implementation
    assert response.status_code == status.HTTP_404_NOT_FOUND
    assert response.json() == { "detail": "Todo not found" }

```

Let's run the test:

```sh
 pytest --disable-warnings
================================================================== test session starts ===================================================================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 4 items                                                                                                                                        

TodoApp/test/test_main.py .                                                                                                                        [ 25%]
TodoApp/test/test_todos.py ...                                                                                                                     [100%]

============================================================== 4 passed, 1 warning in 0.48s ==============================================================

```
