---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 29: Admin router

We have added authentication to all endpoints related to todo so that users can only query their own todo.

Now we need to create some endpoints for admin, these endpoints can only be used by admin, can be used to query all todos and delete todos of anyone.

First, we need to add the role information to the token payload when creating the token.
```py
# auth.py
...

def create_access_token(username: str, user_id: int, role: str, expire_delta: timedelta):
#                                                    ^^^^^^^^^
    to_encode = {"sub": username, "user_id": user_id, "role": role}
    #                                                 ^^^^^^^^^^^^ add role to payload when creating token
    expire = datetime.now(timezone.utc) + expire_delta
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

oauth2_bearer = OAuth2PasswordBearer(tokenUrl='auth/token')

async def get_current_user(token: Annotated[str, Depends(oauth2_bearer)]):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str | None = payload.get("sub")
        user_id: int | None = payload.get("user_id")
        role: str | None = payload.get("role") # extract role from payload
        if username is None or user_id is None:
            raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                                detail="Could not validate credentials")
        return {"username": username, "id": user_id, "role": role}
        #                                            ^^^^^^^^^^^^ now this function will return role as well
    except JWTError:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                            detail="Could not validate credentials")

...

```

Then, create a new router file `routers/admin.py` and copy some codes of router fundamental (imports, session, dependencies...) to this file.

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── __init__.py
    ├── database.py
    ├── main.py
    ├── models.py
    ├── routers
    │   ├── __init__.py
    │   ├── admin.py # new file, to add admin endpoints
    │   ├── auth.py
    │   └── todos.py
    └── todosapp.db
```

```py
# routers/admin.py
from typing import Annotated
from fastapi import APIRouter, Depends, HTTPException, Path
from sqlalchemy.orm import Session
from database import SessionLocal
from starlette import status
import models
from .auth import get_current_user


router = APIRouter(prefix='/admin', tags=['admin'])
#                          ^^^^^^          ^^^^^

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

db_dependency = Annotated[Session, Depends(get_db)]
user_dependency = Annotated[dict, Depends(get_current_user)]

```

And connect the router to the main app.

```py
# main.py
from fastapi import FastAPI
import models
from database import engine
from routers import auth, todos, admin


app = FastAPI()

models.Base.metadata.create_all(bind=engine)

app.include_router(auth.router)
app.include_router(todos.router)
app.include_router(admin.router) # connect the admin router to the main app
```

Now, let's create some endpoints for admin.

```py
# routers/admin.py

...

@router.get("/todos")
async def read_all(user: user_dependency, db: db_dependency):
    if user is None or user.get("role") != "admin":
    #                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^ check if user is admin
        raise HTTPException(status_code=401, detail="Unauthorized to view todos")
    return db.query(models.Todos).all() # query all todos no matter who created it

@router.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(user: user_dependency, db: db_dependency, todo_id: int = Path(gt=0)):
    if user is None or user.get("role") != "admin":
    #                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^ check if user is admin
        raise HTTPException(status_code=401, detail="Unauthorized")
    
    todo = db.query(models.Todos).filter(models.Todos.id == todo_id).first() # query todo by id, no matter who created it
    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")
    
    db.delete(todo)
    db.commit()
```

You can compare the code above with the code in [[2025-03-28_TODOS-project-25:-Authenticate-GET-request-to-query-all-todos-for-logged-user|TODOS project 25: Authenticate GET request to query all todos for logged user]] and [[2025-03-28_TODOS-project-28:-Authenticate-DELETE-request-to-delete-specific-todo|TODOS project 28: Authenticate DELETE request to delete specific todo]] to see the differences.

