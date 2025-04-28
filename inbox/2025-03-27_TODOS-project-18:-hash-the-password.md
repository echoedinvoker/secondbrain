---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 18: hash the password

In [[2025-03-27_TODOS-project-17:-endpoint-to-create-new-user|TODOS project 17: endpoint to create new user]], we created the ORM model `Users` from the pydantic model `CreateUserRequest`, but we didn't hash the password. So, we want to hash the password now.

We will use the `bcrypt` library to hash the password, it is a popular library for hashing passwords. 

```sh
 pip install passlib # we'll use passlib to provide more secure hashing
 pip install bcrypt==4.0.1 # we'll use bcrypt to hash the password, it must be version 4.0.1 in order to work with passlib
                            # version 4.0.1 is important because it is the only version that works with passlib
```

```py
# auth.py
from fastapi import APIRouter
from pydantic import BaseModel
from models import Users
from passlib.context import CryptContext # import CryptContext from passlib


router = APIRouter()

# create a CryptContext object which will be used to hash the password
bcrypt_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
#                             ^^^^^^^^^^^^^^^^^^ use bcrypt algorithm to hash the password

class CreateUserRequest(BaseModel):
    username: str
    email: str
    first_name: str
    last_name: str
    password: str
    role: str

@router.post("/auth")
async def create_user(request: CreateUserRequest):
    created_user = Users(
        username=request.username,
        email=request.email,
        first_name=request.first_name,
        last_name=request.last_name,
        hashed_password=bcrypt_context.hash(request.password),
        #               ^^^^^^^^^^^^^^^^^^^^ use `hash` method of bcrypt_context to hash the password
        role=request.role
    )

    return created_user
```

Let's try to create a new user with the endpoint `/auth` and see if the password is hashed.

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
Then, we got the response with status code 200 and response body:

```json
{
  "username": "matt",
  "email": "matt@mail.com",
  "first_name": "matt",
  "last_name": "chang",
  "hashed_password": "$2b$12$hIe6kEtEQG9ffnQTdrjwHe.QD3KvUSplt3HE6HldlaQ4ug167DSai",
  "role": "admin"
}
```

You can see that the password is hashed in the `hashed_password` field.
