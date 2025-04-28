---
date: 2025-03-27
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 20: Authenticate user

We want to create an endpoint to authenticate a user. Let's create a simple endpoint at first.

```py
# auth.py
...

@router.post("/token")
#       ^^^^  ^^^^^^ when the user is authenticated, we will return a token, so name the endpoint as `/token`
#       ^^^^ this endpoint will be a POST request
async def login_for_access_token():
    return 'token' # currently, we are returning a dummy string for the testing purpose

```

We want to let this endpoint accept a form, and it's not regular FastAPI form but Oath2PasswordRequestForm, which is more secure.

For using OAuth2PasswordRequestForm, we need to install `python-multipart` package.

```sh
pip install python-multipart
```

And OAuth2PasswordRequestForm need to use dependency injection to implement.

```py
# auth.py
...
from fastapi.security import OAuth2PasswordRequestForm

...

@router.post("/token")
async def login_for_access_token(form_data: Annotated[OAuth2PasswordRequestForm, Depends()])
#                                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                                use dependency inject on OAuth2PasswordRequestForm
    return {
        "username": form_data.username,
        "password": form_data.password
    }
```

Let's check the endpoint on the Swagger UI, you can see the requst body is now a form and there are lots of fields in the form. We only care about `username` and `password` fields. Try to send a request with the form data with `username` and `password` fields.

```sh
curl -X 'POST' \
  'http://localhost:8000/token' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=password&username=aa&password=bb&scope=&client_id=string&client_secret=string'
```
We set the `username`=aa and `password`=bb, and the response is:

```json
{
  "username": "aa",
  "password": "bb"
}
```

So, we can get the `username` and `password` from the form data and use them in the endpoint function. Now, we need to query the user by the `username` and use bcrypt to compare the password.

```py
...

# create a function to authenticate the user, return boolean value
def authenticate_user(username: str, password: str, db: Session):
    user = db.query(Users).filter(Users.username == username).first() # query the user by the username
    # if the user is not found, return False
    if not user:
        return False
    # if the password is not matched, return False
    if not bcrypt_context.verify(password, user.hashed_password):
    #      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use `bcrypt_context.verify` to compare the password,
    #                                                            it'll hash the password and compare it with the hashed password in the database
        return False
    return True # if the user is found and the password is matched, return True


bcrypt_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

...

@router.post("/token")
async def login_for_access_token(form_data: Annotated[OAuth2PasswordRequestForm, Depends()],
                                 db: db_dependency):

    # use the authenticate_user function to authenticate the user, and get the result (boolean value)
    result = authenticate_user(form_data.username, form_data.password, db)

    # here we simply return the string based on the result for the testing purpose
    # we'll implement the token generation later
    if not result:
        return 'Failed to authenticate'
    return 'Successfully authenticated'
```

Now, you can test the endpoint with the username and password. If the username and password are correct, you'll get the response `Successfully authenticated`, otherwise, you'll get `Failed to authenticate`. In the final implementation, we'll generate a token and return it to the user. But for now, we'll stop here.
