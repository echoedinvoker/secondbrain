---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 35: Resolve Root Package Issue

```sh
 uvicorn TodoApp.main:app --reload
...
  File "/home/matt/Projects/fastapi/TodoApp/main.py", line 2, in <module>
    import models
ModuleNotFoundError: No module named 'models'
```

We need to change to use relative imports on our own modules.

```py
from fastapi import FastAPI
# import models
from .models import Base
#    ^ relative import from the same directory
from .database import engine
#    ^
from .routers import auth, todos, admin
#    ^


app = FastAPI()

# models.Base.metadata.create_all(bind=engine)
Base.metadata.create_all(bind=engine) # `models` is not needed anymore

app.include_router(auth.router)
app.include_router(todos.router)
app.include_router(admin.router)
```

Check all the imports in the all the files in the project and change them to relative imports. (only fro our own modules)
