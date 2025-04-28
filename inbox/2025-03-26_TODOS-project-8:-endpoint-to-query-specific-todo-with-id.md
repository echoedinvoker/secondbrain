---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 8: endpoint to query specific todo with id

```py
from typing import Annotated
from fastapi import Depends, FastAPI, HTTPException, Path
from sqlalchemy.orm import Session
import models
from database import SessionLocal, engine


app = FastAPI()

models.Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

db_dependency = Annotated[Session, Depends(get_db)]

@app.get("/todos")
async def read_all(db: db_dependency):
    return db.query(models.Todos).all()

# New endpoint to query specific todo with id
@app.get("/todos/{todo_id}")
#                ^^^^^^^^^ dynamic path
async def read_todo(db: db_dependency, todo_id: int = Path(gt=0)):
#                                      ^^^^^^^^^^^^ ^^^^^^^^^^^^ add validation to ensure todo_id is greater than 0
#                                      ^^^^^^^^^^^^ path parameter
    todo = db.query(models.Todos).filter(models.Todos.id == todo_id).first()
    #      ^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^ because fastAPI doesn't know how many rows will be returned
    #      ^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^ we use .first() to get the first row and return it to increase performance
    #      ^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^ it'll effect `filter` to return only one row
    #      ^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ filter queried todos by id
    #      ^^^^^^^^^^^^^^^^^^^^^^ query all todos

    # return todo if it exists
    if todo is not None:
        return todo

    # return 404 if todo doesn't exist, we use HTTPException to return a 404 status code
    raise HTTPException(status_code=404, detail="Todo not found")

```
