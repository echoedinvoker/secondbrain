---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 19: save new user in the database

In [[2025-03-27_TODOS-project-18:-hash-the-password|TODOS project 18: hash the password]], we have already prepared the `Users` object. Now we can save it in the database.

We need to create a session to interact with the database like we did in [[2025-03-26_TODOS-project-7:-dependencies-inject-and-query-all-todos-from-FastAPI|TODOS project 7: dependencies inject and query all todos from FastAPI]]

```py
```py
# auth.py
from fastapi import APIRouter, Depends
#                              ^^^^^^^ import
from pydantic import BaseModel
from models import Users
from passlib.context import CryptContext
from typing import Annotated # import
from database import SessionLocal # import
from sqlalchemy.orm import Session # import
from starlette import status # import 

router = APIRouter()

# create a dependency to get the database session and close it after the request
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# use a variable to store the type of `db` to simplify the code
db_dependency = Annotated[Session, Depends(get_db)]

bcrypt_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class CreateUserRequest(BaseModel):
    username: str
    email: str
    first_name: str
    last_name: str
    password: str
    role: str

@router.post("/auth", status_code=status.HTTP_201_CREATED)
#                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ specify the status code of the response
async def create_user(db: db_dependency, request: CreateUserRequest):
#                     ^^^^^^^^^^^^^^^^^ add the dependency to get the database session `db`
    created_user = Users(
        username=request.username,
        email=request.email,
        first_name=request.first_name,
        last_name=request.last_name,
        hashed_password=bcrypt_context.hash(request.password),
        role=request.role
    )

    # use the session to add the user to the database
    db.add(created_user)
    db.commit()

```
Let's create a new user with curl and check it with sqlite3:

```sh
curl -X 'POST' \
  'http://localhost:8000/auth' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "username": "matt",
  "email": "matt@mail.com",
  "first_name": "matt",
  "last_name": "chang",
  "password": "1234",
  "role": "admin"
}'
```
Now, we got a response with status code 201 Created and the request body is null.

Let's check the database with sqlite3:

```sh
 sqlite3 todosapp.db 
sqlite> SELECT * FROM users;
[{"id":1,"email":"matt@mail.com","username":"matt","first_name":"matt","last_nam
e":"chang","hashed_password":"$2b$12$18gENL.FKjpc1WnRI46R9u8ZxWDwNsldPN2jP4XuG.7
OZOWMYIfbO","is_active":1,"role":"admin"}]
```
You can see that the database added the field `is_active` with the value `1` and a incremented `id` field automatically.
