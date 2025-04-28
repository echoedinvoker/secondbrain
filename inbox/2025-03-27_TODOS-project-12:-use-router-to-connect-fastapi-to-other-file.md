---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 12: use router to connect fastapi to other file

We have already completed multiple endpoints related to todos. Now we want to add endpoints related to authentication, but this will make our `main.py` file too large. Therefore, we are considering creating a new file to handle authentication endpoints.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── __init__.py
    ├── auth.py # new file, will contain authentication endpoints
    ├── database.py
    ├── main.py
    ├── models.py
    ├── __pycache__
    └── todos.db
```
```py
# auth.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/auth/")
async def get_auth():
    return {"message": "You are authenticated"}
```

We have set up a dummy endpoint `/auth/` as shown in `auth.py`. Then, launch the server and check if the endpoint is working.

You'll only see the todos endpoints working because `auth.py` is actually another independent FastAPI application. We need to use a router to connect it to the main FastAPI application instead of running it as a separate application.

```py
# auth.py

# from fastapi import FastAPI
# app = FastAPI() 

# instead of creating a new FastAPI instance,
# we will create a router instance

from fastapi import APIRouter

router = APIRouter() # create a router instance, which is used to connect to the main FastAPI application

@router.get("/auth/")
#^^^^^^ instead of app, we use router to define the endpoint
async def get_auth():
    return {"message": "You are authenticated"}
```

Now, we need to import the router in the `main.py` file and include it in the main FastAPI application.

```py
...
import auth # import the auth file


app = FastAPI()

models.Base.metadata.create_all(bind=engine)

app.include_router(auth.router) # include the router in the main FastAPI application

...

```

Launch the server and check if the `/auth/` endpoint is working. It should work now.

So, routers can help you to organize your code better by splitting it into multiple files and connecting them to the main FastAPI application.
