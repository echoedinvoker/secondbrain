---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 52: test to the function to create token

In [[2025-04-01_TODOS-project-51:-test-to-authenticate-user-function|TODOS project 51: test to authenticate user function]], we've test the function to authenticate user. Now, we will test the function to create token.

First, we check the function `create_access_token` in `TodoApp/routers/auth.py`:
```py
...
from datetime import datetime, timedelta, timezone
from jose import jwt, JWTError

router = APIRouter(prefix='/auth', tags=['auth'])

SECRET_KEY = 'daa98655a0d8c4e14fdc77f87afaa9c61b7e413df6a509be38f7a82fbf9ffad0'
ALGORITHM = 'HS256'

...

def authenticate_user(username: str, password: str, db: Session):...

def create_access_token(username: str, user_id: int, role: str, expire_delta: timedelta):
    to_encode = {"sub": username, "user_id": user_id, "role": role}
    expire = datetime.now(timezone.utc) + expire_delta
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

...

```

It's a regular function, so we can simply test it by calling it with the correct arguments and check the result.

```py
# TodoApp/routers/auth.py
from .utils import *
from ..routers.auth import authenticate_user, get_db, create_access_token, SECRET_KEY, ALGORITHM
#                                                     ^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^ for decode token
#                                                     ^^^^^^^^^^^^^^^^^^^ the function to test
from datetime import timedelta # for expire_delta
from jose import jwt # for decode token


...

    
def test_create_access_token():
    # prepare the arguments for the function `create_access_token`
    username = "matt"
    user_id = 1
    role = "admin"
    expire_delta = timedelta(days=1)

    # call the function `create_access_token` to get the token
    token = create_access_token(username, user_id, role, expire_delta)

    # decode the token to get the payload
    payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM],
                         options={"verify_exp": False})

    # check the payload if it is correct
    assert payload["sub"] == username
    assert payload["user_id"] == user_id
    assert payload["role"] == role
```


