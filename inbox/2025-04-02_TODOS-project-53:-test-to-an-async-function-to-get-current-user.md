---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 53: test to an async function to get current user

Before we start, let's check the function that we want to test:

```py
...
from starlette import status
from typing import Annotated
from fastapi.security import OAuth2PasswordRequestForm, OAuth2PasswordBearer
from jose import jwt, JWTError

oauth2_bearer = OAuth2PasswordBearer(tokenUrl='auth/token')

async def get_current_user(token: Annotated[str, Depends(oauth2_bearer)]):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str | None = payload.get("sub")
        user_id: int | None = payload.get("user_id")
        role: str | None = payload.get("role")
        if username is None or user_id is None:
            raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                                detail="Could not validate credentials")
        return {"username": username, "id": user_id, "role": role}
    except JWTError:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                            detail="Could not validate credentials")

...

```

This is an async function that takes a token as an argument and returns the current user, so we can test this function by creating a token and passing it to the function to get the current user and assert the result.

```py
from ..routers.auth import authenticate_user, get_db, create_access_token, SECRET_KEY, ALGORITHM, get_current_user
#                                                                                                 ^^^^^^^^^^^^^^^^ import the function to test


...

async def test_get_current_user(): # because `get_current_user` is an async function, so the test function should be async too

    # create a token
    to_encode = {"sub": "matt", "user_id": 1, "role": "admin"}
    token = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

    # use `get_current_user` function to decode the token and get the current user
    current_user = await get_current_user(token)
    #              ^^^^^ because `get_current_user` is an async function, so we should use `await`

    # assert the current user
    assert current_user == {"username": "matt", "id": 1, "role": "admin"}

```

Let's run the test:

```sh
 pytest --disable-warnings -vv
============================================= test session starts ==============================================
...
TodoApp/test/test_auth.py::test_get_current_user SKIPPED
(async def function and no async plugin installed (see warnings))                                         [ 30%]
...

================================== 19 passed, 1 skipped, 2 warnings in 2.67s ===================================
```

We found that the test is skipped because by default, pytest does not support async functions. To fix this, we need to install the `pytest-asyncio` plugin:

```sh
pip install pytest-asyncio
```

Then, we need to use `pytest` to mark the test function as an async function:

```py
...
import pytest # import pytest

...

@pytest.mark.asyncio # mark the test function as an async function
async def test_get_current_user():
    to_encode = {"sub": "matt", "user_id": 1, "role": "admin"}
    token = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    current_user = await get_current_user(token)
    assert current_user == {"username": "matt", "id": 1, "role": "admin"}

```

Now, the test should run successfully.



