---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 17: endpoint to create new user

We want to create an endpoint to create a new user. The request should have the request body, so the endpoint should be a POST request and there should be a pydantic model to validate the request body.

```py
# auth.py
from fastapi import APIRouter
from pydantic import BaseModel # import
from models import Users # import


router = APIRouter()

# create a pydantic model to validate the request body
class CreateUserRequest(BaseModel):
    # `id` is not required because it is autoincremented
    username: str
    email: str
    first_name: str
    last_name: str
    password: str # in ORM model, it is `hashed_password`, but in request body, it is `password` because it is not hashed yet
    role: str
    # `is_active` is not required because it is set to `True` by default in the ORM model

@router.post("/auth")
#       ^^^^
async def create_user(request: CreateUserRequest):
#                              ^^^^^^^^^^^^^^^^^ use pydantic model to accept request body and validate it

    created_user = Users(**request.model_dump()) # try to use similar way to convert pydantic model to ORM model

```

When the endpoint is called, the error happens because the `Users` model does not have the `password` field and the `hashed_password` field is not set. So we cannot use `model_dump()` way to convert the pydantic model to the ORM model. We should set each field manually.

```py
from fastapi import APIRouter
from pydantic import BaseModel
from models import Users


router = APIRouter()

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
        hashed_password=request.password, # we'll introduce how to hash the password later, for now, we'll use the plain password for testing
        role=request.role
    )

    # for now, we just return the created user for testing, we'll introduce how to save the user to the database later
    return created_user
```

Now, we can launch the FastAPI server and test the endpoint using the following curl command:

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
Then, we got the response with the status code `200 OK` and the response body as follows:
```json
{
  "username": "matt",
  "email": "matt@mail.com",
  "first_name": "matt",
  "last_name": "chang",
  "hashed_password": "1234",
  "role": "admin"
}
```
You can see the `id` and `is_active` fields are not included in the response because creating ORM model instance does not add these fields, they will be added when the user is saved to the database.

