---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 7: dependencies inject and query all todos from FastAPI

We need to create an endpoint in the FastAPI app to query all todos from the database.

```py
# main.py
from typing import Annotated
from fastapi import Depends, FastAPI
from sqlalchemy.orm import Session
import models
from database import SessionLocal, engine


app = FastAPI()

models.Base.metadata.create_all(bind=engine)

# create a dependency to get the database session and close it after the request
def get_db():
    db = SessionLocal() # create a new session
    try:
        yield db # provide the session to the request
    #   ^^^^^ instead of `return`, we use `yield` to return the session
    #         because we want to close the session after the request is done
    #         so this should be a generator function
    finally:
        db.close() # close the session after the request is done

# create an endpoint to query all todos
@app.get("/")
async def read_all(db: Annotated[Session, Depends(get_db)]):
    #                  ^^^^^^^^^ ^^^^^^^  ^^^^^^^^^^^^^^^ `Depends` is used to inject the dependency, means get_db function will be called before this function
    #                  ^^^^^^^^^ ^^^^^^^ `Session` is the type of returned value from get_db function
    #                  ^^^^^^^^^ `Annotated` is used to provide type hints with additional context
    # so before executing the read_all function, get_db function will be called to get the database session `db`
    # then, we can use this session to query all todos
    return db.query(models.Todos).all() # query all todos from the database
    #               ^^^^^^^^^^^^ model `Todos` is used to query all todos
```

We will create other endpoints later to handle other operations related to the database. It is necessary to open a session with the database before performing any operations, and close the session after finishing the operations. Therefore, we can assign `Annotated[Session, Depends(get_db)]` to a variable to simplify the code.

```py
...

db_dependency = Annotated[Session, Depends(get_db)] # assign the type of `db` to a variable

@app.get("/")
async def read_all(db: db_dependency):
#                      ^^^^^^^^^^^^^ use the variable to simplify the code
    return db.query(models.Todos).all()
```
