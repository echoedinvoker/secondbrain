---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 38: mock user and override dependency

In [[2025-03-31_TODOS-project-37:-mock-database-and-override-dependency|TODOS project 37: mock database and override dependency]], we've mocked the database and overridden the dependency to create the session. Now, we'll mock the user and override the dependency to get the user.

Before we start, let's see the endpoint that we'll be working on:

```py
# TodoApp/routers/todos.py

...

db_dependency = Annotated[Session, Depends(get_db)]
user_dependency = Annotated[dict, Depends(get_current_user)]
#                                         ^^^^^^^^^^^^^^^^ dependency to get the user
#                                                          it decode the token and get the user information from it
#                                                          and finally return the user information like:
#                                                          {"username": username, "id": user_id, "role": role}

class TodoRequest(BaseModel):...

@router.get("/todos")
async def read_all(user: user_dependency, db: db_dependency):
#                  ^^^^^^^^^^^^^^^^^^^^^ get user information by injecting the dependency

...

```

Create a override dependency to mock the user in our test file:

```py
...
from ..routers.todos import get_db, get_current_user
#                                   ^^^^^^^^^^^^^^^^ import the original dependency

...

def override_get_db():...

# create a override dependency to mock the user
def override_get_current_user():
    return {"username": 'matt', "id": 1, "role": 'admin'} # directly return the mock user information instead of decoding the token

app.dependency_overrides[get_db] = override_get_db
app.dependency_overrides[get_current_user] = override_get_current_user # override the dependency `get_current_user` with `override_get_current_user`

```
