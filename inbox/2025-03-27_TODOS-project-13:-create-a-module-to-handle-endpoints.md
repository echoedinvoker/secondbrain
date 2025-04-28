---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 13: create a module to handle endpoints

In [[2025-03-27_TODOS-project-12:-use-router-to-connect-fastapi-to-other-file|TODOS project 12: use router to connect fastapi to other file]], we've learned how to use routers to connect FastAPI to other files. We can further improve our code by creating a module to handle endpoints.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── database.py
    ├── __init__.py
    ├── main.py
    ├── models.py
    ├── __pycache__
    ├── routers # create a new directory for endpoints
    │   ├── __init__.py # make this directory a module
    │   └── auth.py # move `auth.py` into this directory
    └── todos.db
```

Because we moved `auth.py` into the `routers` directory, we need to update the import statement in `main.py`:

```python
# main.py
...
from routers import auth # update the import statement

...

```

We can refactor the codes related to the *todos* endpoints in the other file inside the `routers` directory.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── database.py
    ├── __init__.py
    ├── main.py
    ├── models.py
    ├── __pycache__
    ├── routers
    │   ├── __init__.py
    │   ├── auth.py
    │   └── todos.py # create a new file for todos endpoints
    └── todos.db
```

```py
# todos.py
from typing import Annotated
from fastapi import APIRouter, Depends, HTTPException, Path
from sqlalchemy.orm import Session
from database import SessionLocal
from pydantic import BaseModel, Field
from starlette import status
import models


router = APIRouter()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

db_dependency = Annotated[Session, Depends(get_db)]

class TodoRequest(BaseModel):
    title: str = Field(min_length=3)
    description: str = Field(min_length=3, max_length=100)
    priority: int = Field(ge=1, le=5)
    completed: bool


@router.get("/todos")
async def read_all(db: db_dependency):
    return db.query(models.Todos).all()

@router.get("/todos/{todo_id}")
async def read_todo(db: db_dependency, todo_id: int):
    todo = db.query(models.Todos).filter(models.Todos.id == todo_id).first()

    if todo is not None:
        return todo

    raise HTTPException(status_code=404, detail="Todo not found")

@router.post("/todos")
async def create_todo(db: db_dependency,new_todo: TodoRequest):
    todo = models.Todos(**new_todo.model_dump())
    db.add(todo)
    db.commit()

@router.put("/todos/{todo_id}")
async def update_todo(db: db_dependency,
                      new_todo: TodoRequest,
                      todo_id: int = Path(gt=0)):
    todo = db.query(models.Todos).filter(models.Todos.id == todo_id).first()

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    setattr(todo, "title", new_todo.title)
    setattr(todo, "description", new_todo.description)
    setattr(todo, "priority", new_todo.priority)
    setattr(todo, "completed", new_todo.completed)

    db.add(todo)
    db.commit()

@router.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(db: db_dependency, todo_id: int = Path(gt=0)):
    query = db.query(models.Todos).filter(models.Todos.id == todo_id)
    todo = query.first()

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    query.delete()
    db.commit()

```

Then, include the router of `todos.py` in `main.py`:

```py
from fastapi import FastAPI
import models
from database import engine
from routers import auth, todos
#                         ^^^^^ import


app = FastAPI()

models.Base.metadata.create_all(bind=engine)

app.include_router(auth.router)
app.include_router(todos.router) # include the router of todos.py

```

You can see that the code is more organized and easier to maintain.


