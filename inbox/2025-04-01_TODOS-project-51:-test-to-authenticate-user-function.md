---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 51: test to authenticate user function

Before we start, we can check the authenticate_user function in the auth.py file.
```py
...

from passlib.context import CryptContext

bcrypt_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

db_dependency = Annotated[Session, Depends(get_db)]


def authenticate_user(username: str, password: str, db: Session):
    user = db.query(Users).filter(Users.username == username).first()
    if not user:
        return False
    if not bcrypt_context.verify(password, user.hashed_password):
        return False
    return user

...

```

It's a regular function instead of endpoint. So, we can directly call the function and test it.

```py
# TodoApp/tests/test_auth.py
from .utils import *
from ..routers.auth import authenticate_user, get_db
#                          ^^^^^^^^^^^^^^^^^  ^^^^^^ only one dependency need to be overriden
#                          ^^^^^^^^^^^^^^^^^ the function we want to test

app.dependency_overrides[get_db] = override_get_db

# test authenticate_user function
def test_authenticate_user(test_user):
#                          ^^^^^^^^^ insert a user by fixture
    db = TestingSessionLocal()
    authenticated_user = authenticate_user("matt", "password", db) # test the function with fixture user name and password
    assert authenticated_user.username == "matt" 
```

Above test should be passed. We can further test the edge cases like wrong password, wrong username in the same function.

```py
...

def test_authenticate_user(test_user):
    db = TestingSessionLocal()
    authenticated_user = authenticate_user("matt", "password", db)
    assert authenticated_user.username == "matt" # type: ignore

    # further test the same function with wrong username and password
    wrong_username = authenticate_user("wrong_name", "password", db)
    #                                   ^^^^^^^^^^ wrong username
    assert wrong_username is False

    wront_password = authenticate_user("matt", "wrong_password", db)
    #                                           ^^^^^^^^^^^^^^ wrong password
    assert wront_password is False
    
```


