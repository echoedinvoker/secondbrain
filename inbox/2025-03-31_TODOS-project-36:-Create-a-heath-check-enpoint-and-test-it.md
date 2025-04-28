---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 36: Create a heath check enpoint and test it

We want to create a very simple endpoint, which just returns a JSON response with a status message. We can use it to check if our application is running correctly and if the client can connect to it.

```py
# TodoApp/main.py
from fastapi import FastAPI
from .models import Base
from .database import engine
from .routers import auth, todos, admin


app = FastAPI()

Base.metadata.create_all(bind=engine)

# Health check endpoint
@app.get("/healthy")
def healthy_check():
    return {"status": "healthy"}

app.include_router(auth.router)
app.include_router(todos.router)
app.include_router(admin.router)
```

Then, we can create a file `test_main.py` and write a test for the health check endpoint:

```py
# TodoApp/test/test_main.py (new file)
from fastapi.testclient import TestClient # we can initiate a test client to test our endpoints
from ..main import app
from starlette import status


client = TestClient(app) # create a test client

# Test the health check endpoint
def test_return_health_check():
    response = client.get("/healthy") # send a get request to the /healthy endpoint from the test client
    assert response.status_code == status.HTTP_200_OK
    assert response.json() == {"status": "healthy"}
```

```sh
 pytest
============================= test session starts ==============================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 1 item                                                               

TodoApp/test/test_main.py .                                              [100%]

=============================== warnings summary ===============================
TodoApp/database.py:14
  /home/matt/Projects/fastapi/TodoApp/database.py:14: MovedIn20Warning: The ``de
clarative_base()`` function is now available as sqlalchemy.orm.declarative_base(
). (deprecated since: 2.0) (Background on SQLAlchemy 2.0 at: https://sqlalche.me
/e/b8d9)
    Base = declarative_base()

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
========================= 1 passed, 1 warning in 0.40s =========================
```

There a warning about the `declarative_base` function, that is library related and not our code. We can ignore it by `--disable-warnings` flag:

```sh
 pytest --disable-warnings
#        ^^^^^^^^^^^^^^^^^^
============================= test session starts ==============================
platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
rootdir: /home/matt/Projects/fastapi
plugins: anyio-4.8.0
collected 1 item                                                               

TodoApp/test/test_main.py .                                              [100%]

========================= 1 passed, 1 warning in 0.40s =========================

```



