---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 22: Get token from request and Decode a JWT

When using JWT, decoding JWT to obtain user information is required in every endpoint that needs authentication. Therefore, we write it in a regular function instead of repeating it in every endpoint.

We use `fastapi.security` to retrieve tokens from requests, we need to apply it using dependency injection.

```py
# auth.py
...
from starlette import status
from fastapi.security import OAuth2PasswordRequestForm, OAuth2PasswordBearer
#                                                       ^^^^^^^^^^^^^^^^^^^^ OAuth2PasswordBearer is a class that will be used to create a dependency
#                                                                            this dependency will get the token from the target parameter
from jose import jwt, JWTError
from starlette import status

...

# OAuth2PasswordBearer is a class that will be used to create a dependency
oauth2_bearer = OAuth2PasswordBearer(tokenUrl='token')
#                                              ^^^^^ target to endpoint used to generate the token

# create a reuseable function to decode the token and return the user information, it will be used in every endpoint that needs authentication
async def get_current_user(token: Annotated[str, Depends(oauth2_bearer)]):
#                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use injection dependency to get the token

    # `jwt.decode` may raise an exception if the token is invalid, so we need to catch it
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM]) # decode the token

        # get the user information from the payload
        username: str | None = payload.get("sub")
        user_id: int | None = payload.get("id")

        # sometimes even the token is valid, the payload may not contain the user information, it still be counted as invalid
        # so we need to response 401 if the user information is not found
        if username is None or user_id is None:
            raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                                detail="Could not validate credentials")

    # if the token is invalid, it'll raise an exception JWTError, we catch it and response 401 by HTTPEXception
    except JWTError:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                            detail="Could not validate credentials")

...

```
