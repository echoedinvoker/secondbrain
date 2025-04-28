---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 46: tests for admin endpoints

We want to create a new file with tests for the admin endpoints.

```sh
 tree
.
├── fastapienv
├── testdb.db
└── TodoApp
    ├── __init__.py
    ├── alembic
    ├── alembic.ini
    ├── database.py
    ├── main.py
    ├── models.py
    ├── routers
    └── test
        ├── __init__.py
        ├── test_admin.py # new file
        ├── test_main.py
        ├── test_todos.py
        └── utils.py
```

Before we start writing the tests:

```py
# TodoApp/test/test_admin.py
from .utils import * # import reusable codes
from ..routers.admin import get_db, get_current_user # import dependencies
from starlette import status

# override dependencies
app.dependency_overrides[get_db] = override_get_db
app.dependency_overrides[get_current_user] = override_get_current_user
#                                            ^^^^^^^^^^^^^^^^^^^^^^^^^ you need to make sure that the override user is an admin
#                                                                      because the admin endpoints only allow admin users
```

Then we can start writing the tests. Let's start with the `GET /admin/todos` endpoint.

```py
# TodoApp/routers/admin.py
@router.get("/todos")
async def read_all(user: user_dependency, db: db_dependency):
    if user is None or user.get("role") != "admin":
        raise HTTPException(status_code=401, detail="Unauthorized to view todos")
    return db.query(Todos).all()
```

Let's write the test for it:

```py
# TodoApp/test/test_admin.py

...

def test_read_all_todos(test_todo):
    response = client.get("/admin/todos")
    assert response.status_code == status.HTTP_200_OK
    assert response.json() == [
        {
            "id": 1,
            "title": "Learn to code!",
            "description": "I want to learn to code in Python",
            "priority": 5,
            "completed": False,
            "owner_id": 1
        }
    ]
```

The point is to make sure that overriding user is an admin. If not, the test will fail.

