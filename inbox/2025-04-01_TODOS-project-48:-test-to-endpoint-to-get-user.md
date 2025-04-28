---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 48: test to endpoint to get user

Before we write the test for the endpoint to get a user, let's change the endpoint:

```py
# TodoApp/routers/users.py
...

router = APIRouter(
    prefix='/user',
    tags=['user']
)


def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()


db_dependency = Annotated[Session, Depends(get_db)]
user_dependency = Annotated[dict, Depends(get_current_user)]

...

@router.get('/', status_code=status.HTTP_200_OK)
async def get_user(user: user_dependency, db: db_dependency):
    if user is None:
        raise HTTPException(status_code=401, detail='Authentication Failed')
    return db.query(Users).filter(Users.id == user.get('id')).first()

...

```

Now, let's write the test for the endpoint to get a user:

```py
# TodoApp/tests/test_users.py
from .utils import *
from ..routers.users import get_db, get_current_user
from starlette import status

app.dependency_overrides[get_db] = override_get_db
app.dependency_overrides[get_current_user] = override_get_current_user

def test_get_user():
    response = client.get("/user")
    assert response.status_code == status.HTTP_200_OK
```

This test should pass, but in fact there is no any user in the MOCK database.

```py
...

app.dependency_overrides[get_db] = override_get_db
app.dependency_overrides[get_current_user] = override_get_current_user

def test_get_user():
    response = client.get("/user")
    assert response.status_code == status.HTTP_200_OK
    assert response.json() is None # Add this line
```

Even we add the line `assert response.json() is None`, the test still passes. This is because the endpoint to get a user from injection dependency `get_current_user` but the database is empty.

We can use fixture to create a user in the MOCK database:

```py
# TodoApp/tests/utils.py
...
from ..models import Todos, Users
#                           ^^^^^
from ..routers.auth import bcrypt_context # to create hashed password

...

# create a fixture to create a user in the MOCK database
@pytest.fixture
def test_user():
    user = Users(
        username="matt",
        email="matt@mail.com",
        first_name="Matt",
        last_name="Chang",
        hashed_password=bcrypt_context.hash("password"), # hash the `password`
        role="admin",
        phone_number="1234567890"
    )
    db = TestingSessionLocal()
    db.add(user)
    db.commit()
    
    yield user
    
    db.query(Users).delete()
    db.commit()

```

Now, we can pass the `test_user` fixture as an argument to the test function:

```py
# TodoApp/tests/test_users.py
...

def test_get_user(test_user):
#                 ^^^^^^^^^ pass fixture as an argument to insert a user to the MOCK database
    response = client.get("/user") # the `get_current_user` return {"username": 'matt', "id": 1, "role": 'admin'}
                                   # so it'll use id=1 to get the user in the MOCK database
    assert response.status_code == status.HTTP_200_OK

    # check the response user content, it should be the same as the user we created in the fixture
    assert response.json()['username'] == 'matt'
    assert response.json()['email'] == 'matt@mail.com'
    assert response.json()['first_name'] == 'Matt'
    assert response.json()['last_name'] == 'Chang'
    # we cannot create the same hashed password string, so just skip it
    assert response.json()['role'] == 'admin'
    assert response.json()['phone_number'] == '1234567890'

```
