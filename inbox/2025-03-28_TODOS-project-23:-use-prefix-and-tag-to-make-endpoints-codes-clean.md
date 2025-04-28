---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 23: use prefix and tag to make endpoints codes clean

In each file about endpoints, we can set a prefix and a tag to make the code clean and easy to read in the Swagger UI.

```py
# auth.py
...

router = APIRouter(prefix='/auth', tags=['auth'])
#                  ^^^^^^^^^^^^^^  ^^^^^^^^^^^^^ on Swagger UI, the endpoints will be under the 'auth' tag, so we can easily find them
#                  ^^^^^^^^^^^^^^ every endpoint path will start with '/auth'

...

@router.post("/", status_code=status.HTTP_201_CREATED)
#            ^^^ add the prefix, so the path will be '/auth/'
async def create_user(db: db_dependency, request: CreateUserRequest):...

@router.post("/token", response_model=Token)
#             ^^^^^^ add the prefix, so the path will be '/auth/token'
async def login_for_access_token(form_data: Annotated[OAuth2PasswordRequestForm, Depends()],
                                 db: db_dependency):...

```

Remember we used OAuth2PasswordBearer to create a dependency for getting the token, and it target to the endpoint path that the endpoint used to generate the token. Now, we need to update the targeted path because we added the prefix.

```py
...

oauth2_bearer = OAuth2PasswordBearer(tokenUrl='auth/token')
#                                              ^^^^^^^^^^ update the path from 'token' to 'auth/token'

```


