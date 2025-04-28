---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 37: mock database and override dependency

We want to create tests for todos enpoints.

Before we can do that, let's check the current implementation of the todos endpoints.

```py
# TodoApp/routers/todos.py

...

# this dependency is used to connect to the database
def get_db():
    db = SessionLocal() # initialize to create a session to connect to the PRODUCTION database
    try:
        yield db
    finally:
        db.close()

db_dependency = Annotated[Session, Depends(get_db)]
#                                          ^^^^^^ we need to override this dependency
...

@router.get("/todos")
async def read_all(user: user_dependency, db: db_dependency):
#                                             ^^^^^^^^^^^^^
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized to view todos")
    return db.query(Todos).filter(Todos.owner_id == user.get("id")).all()

...

```

```py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base


SQLALCHEMY_DATABASE_URL = "postgresql://postgres:1234@localhost:5432/todos" # url of the PRODUCTION database

engine = create_engine(SQLALCHEMY_DATABASE_URL)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine) # return a class that can be used to create a session to connect to the PRODUCTION database

Base = declarative_base()

```

We need to use similar code to mock the database with sqlite and override the `get_db` dependency in the FastAPI app.

Create a mock database file `testdb.db` in the root directory of the project.

```py
# TodoApp/test/test_todos.py (new file)
from sqlalchemy import create_engine
from sqlalchemy.pool import StaticPool
from ..database import Base


SQLALCHEMY_DATABASE_URL = "sqlite:///./testdb.db" # url of the TEST database

engine = create_engine(
    SQLALCHEMY_DATABASE_URL,
    # for sqlite, we need to set connect_args={"check_same_thread": False}
    connect_args={"check_same_thread": False},
    # for sqlite, we need to set poolclass=StaticPool
    poolclass=StaticPool
)

# this `Base` comes from the `database` module, which has all the models defined in the FastAPI app, we don't need to re-create it
Base.metadata.create_all(bind=engine) # when there is no `testdb.db` file, this will create the file and the tables based on the models

```sh
$ pytest

...
$ tree
.
├── fastapienv
├── testdb.db # created by the `Base.metadata.create_all(bind=engine)` call
└── TodoApp
```

But each todos endpoints injects the dependency `get_db` to connect to the database. We need to override this dependency in the FastAPI app.

```py
...
from sqlalchemy.orm import sessionmaker
from ..routers.todos import get_db
from ..main import app

...

# create a new session class that can be used to create a session to connect to the TEST database
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# create a new dependency to override the `get_db` dependency
def override_get_db():
    db = TestingSessionLocal()
    #    ^^^^^^^^^^^^^^^^^^^ instead of SessionLocal, we use TestingSessionLocal to connect to the TEST database
    try:
        yield db
    finally:
        db.close()

# override the `get_db` dependency in the FastAPI app with the above dependency
app.dependency_overrides[get_db] = override_get_db

```

Now, we already have a mock database and we have overridden the `get_db` dependency in the FastAPI app to connect to the mock database. Then, we can create tests for the todos endpoints.
