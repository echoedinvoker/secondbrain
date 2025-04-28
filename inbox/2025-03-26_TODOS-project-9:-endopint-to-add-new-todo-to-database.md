---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 9: endopint to add new todo to database

```py
from typing import Annotated
from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy.orm import Session
import models
from database import SessionLocal, engine
from pydantic import BaseModel, Field


app = FastAPI()

models.Base.metadata.create_all(bind=engine)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

db_dependency = Annotated[Session, Depends(get_db)]

# create a pydantic model for validating the request body of the POST request
class TodoRequest(BaseModel):
    # no id field because it is autoincremented
    title: str = Field(min_length=3)
    description: str = Field(min_length=3, max_length=100)
    priority: int = Field(ge=1, le=5)
    completed: bool

...

# create a POST endpoint to add a new todo to the database
@app.post("/todos", status_code=status.HTTP_201_CREATED)
async def create_todo(db: db_dependency, new_todo: TodoRequest):
#                                                  ^^^^^^^^^^^ use pydantic model to accept request body and validate it
    todo = models.Todos(**new_todo.model_dump()) # turn the pydantic model into a dictionary and then create a Todos object (ORM model) from it
    db.add(todo) # tell the database to prepare to add the new todo
    db.commit() # commit the transaction to add the new todo to the database actually

