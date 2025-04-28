---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 39: Test to endpoint that read all todos

In [[2025-03-31_TODOS-project-37:-mock-database-and-override-dependency|TODOS project 37: mock database and override dependency]], we've created a mock database and override its dependency. In [[2025-03-31_TODOS-project-38:-mock-user-and-override-dependency|TODOS project 38: mock user and override dependency]], we've created a mock user and override its dependency. Now, we can finally test the endpoint that reads all todos.

```py
# TodoApp/tests/test_todos.py
# ...
from fastapi.testclient import TestClient
from starlette import status


# ...(omit the codes to mock database and user)...


client = TestClient(app) # create a test client

# create a test for the endpoint that reads all todos
def test_read_all_authenticated():
    response = client.get("/todos") # send a GET request to /todos from the test client
    assert response.status_code == status.HTTP_200_OK # assert the response status code is 200
    assert response.json() == [] # assert the response body is an empty list (because we haven't created any todo in the MOCK database yet)
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

================================ 2 passed, 1 warning in 0.42s =================================

```



