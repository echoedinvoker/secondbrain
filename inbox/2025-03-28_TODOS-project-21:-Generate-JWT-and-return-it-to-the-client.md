---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 21: Generate JWT and return it to the client

In [[2025-03-27_TODOS-project-20:-Authenticate-user|TODOS project 20: Authenticate user]], we have implemented the user authentication. Now, if the user is authenticated, we need to generate a JWT and return it to the client.

In order to generate a JWT, we need to install the `python-jose[cryptography]` package.
```sh
 pip install "python-jose[cryptography]"
```

```py
...
from datetime import datetime, timedelta, timezone # import
from jose import jwt # import

router = APIRouter()

# in order to generate a JWT, we need to provide a secret key and an algorithm
SECRET_KEY = 'daa98655a0d8c4e14fdc77f87afaa9c61b7e413df6a509be38f7a82fbf9ffad0' # anything is fine, or you can use `openssl rand -hex 32` to generate it
ALGORITHM = 'HS256' # common algorithm to use with JWT

...

def authenticate_user(username: str, password: str, db: Session):
    user = db.query(Users).filter(Users.username == username).first()
    if not user:
        return False
    if not bcrypt_context.verify(password, user.hashed_password):
        return False
    return True

# create a function to generate a JWT
def create_access_token(username: str, user_id: int, expire_delta: timedelta):
    to_encode = {"sub": username, "user_id": user_id} # payload, which includes some user information, when the token is decoded
    expire = datetime.now(timezone.utc) + expire_delta # calculate the expiration time in the future
    to_encode.update({"exp": expire}) # add the expiration time to the payload
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM) # use jose method to encode the payload with the secret key and algorithm
                                                                  # to generate a JWT

...

@router.post("/token")
async def login_for_access_token(form_data: Annotated[OAuth2PasswordRequestForm, Depends()],
                                 db: db_dependency):
    result = authenticate_user(form_data.username, form_data.password, db)
    if not result:
        return 'Failed to authenticate'

    # when the user is authenticated, generate a JWT and return it to the client
    # but function `create_access_token` need user information, we can modify the `authenticate_user` function to return the user object instead of a boolean
    return 'Successfully authenticated'
```

Modify the `authenticate_user` function to return the user object instead of a boolean, then use the user object to generate a JWT and return it to the client.

```py
...

def authenticate_user(username: str, password: str, db: Session):
    user = db.query(Users).filter(Users.username == username).first()
    if not user:
        return False
    if not bcrypt_context.verify(password, user.hashed_password):
        return False
    return user
    #      ^^^^ return the user object instead of a boolean

def create_access_token(username: str, user_id: int, expire_delta: timedelta): ...

...

@router.post("/token")
async def login_for_access_token(form_data: Annotated[OAuth2PasswordRequestForm, Depends()],
                                 db: db_dependency):
    user = authenticate_user(form_data.username, form_data.password, db)
    if not user:
        return 'Failed to authenticate'

    # create a JWT using the user information
    access_token = create_access_token(user.username, user.id, timedelta(minutes=25))

    return access_token
    #      ^^^^^^^^^^^^ instead of string, return the generated JWT
```

Let's test the API:

```sh
curl -X 'POST' \
  'http://localhost:8000/token' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=password&username=matt&password=1234&scope=&client_id=string&client_secret=string'
```
And got the response body as below:
```json
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJtYXR0IiwidXNlcl9pZCI6MSwiZXhwIjoxNzQzMTM1MDY0fQ.qeed_RDHSZj078emiEMc1did9W2c0rBMiXIAoEbilr0"
```

Typically, we'll further wrap the JWT with token type, instead of returning the raw JWT.

```py
...

# create a pydantic model to validate response body
class Token(BaseModel):
    access_token: str
    token_type: str

...

@router.post("/token", response_model=Token)
#                      ^^^^^^^^^^^^^^^^^^^^ use `response_model` and assign the pydantic model to validate the response body
async def login_for_access_token(form_data: Annotated[OAuth2PasswordRequestForm, Depends()],
                                 db: db_dependency):
    user = authenticate_user(form_data.username, form_data.password, db)

    if not user:
        # instead of string, reponse 401 if the user is not authenticated
        raise HTTPEXception(status_code=status.HTTP_401_UNAUTHORIZED, detail="Incorrect username or password") 

    access_token = create_access_token(user.username, user.id, timedelta(minutes=25))

    # to fit the pydantic model of response body, return the JWT with token type
    return {"access_token": access_token, "token_type": "bearer"}
    #                                                    ^^^^^^ this is a common token type for JWT
```
