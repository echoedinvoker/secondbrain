---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 54: test to async function when exception happened

In [[2025-04-02_TODOS-project-53:-test-to-an-async-function-to-get-current-user|TODOS project 53: test to an async function to get current user]], we've test the async function `get_current_user` when it's successful. Now, we want to test it when an exception happened.

Before we start, let's check the code of the async function `get_current_user`:

```py
# TodoApp/routers/auth.py

...

async def get_current_user(token: Annotated[str, Depends(oauth2_bearer)]):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str | None = payload.get("sub")
        user_id: int | None = payload.get("user_id")
        role: str | None = payload.get("role")
        if username is None or user_id is None:
            raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, # <--- we want to test this line
                                detail="Could not validate credentials")
        return {"username": username, "id": user_id, "role": role}
    except JWTError:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                            detail="Could not validate credentials")

...

```

Now, let's write the test for the async function `get_current_user` when an exception happened:

```py
# TodoApp/tests/test_auth.py

...
from fastapi import HTTPException # because we want to test HTTPException happened
from ..routers.auth import authenticate_user, get_db, create_access_token, SECRET_KEY, ALGORITHM, get_current_user
#                                                                                                 ^^^^^^^^^^^^^^^^ async function to test
import pytest # we need to use pytest.mark.asyncio to mark the test as async for testing async function

...

@pytest.mark.asyncio # mark the test as async
async def test_get_current_user_missing_payload():
    to_encode = {"role": "admin"} # we don't include "sub" and "user_id" in the payload, so `get_current_user` will raise an exception
    token = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM) # create a token with the payload that doesn't include "sub" and "user_id"

    with pytest.raises(HTTPException) as exc: # in order to catch the exception, we use `pytest.raises` context manager to wrap the code that will raise the exception
        await get_current_user(token) # call the async function `get_current_user` with the token that doesn't include "sub" and "user_id"
                                      # because it's wrapped in `pytest.raises` context manager, it will catch the exception and store it in `exc`

    # if the exception is raised, `exc` will store the exception, so we can check the exception's status_code and detail
    assert exc.value.status_code == 401
    assert exc.value.detail == "Could not validate credentials"

```
