---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 25: Authenticate GET request to query all todos for logged user

In [[2025-03-26_TODOS-project-7:-dependencies-inject-and-query-all-todos-from-FastAPI|TODOS project 7: dependencies inject and query all todos from FastAPI]], we've created an endpoint to query all todos. But now, we want this endpoint to query only the todos of the logged user instead of all todos. And this request should be authenticated.

Similar to [[2025-03-28_TODOS-project-24:-authenticate-post-request-to-create-todo|TODOS project 24: authenticate post request to create todo]], we inject `get_current_user` dependency to get the information of logged user and authenticate the request. Then, use the user id from it to query only the todos of the logged user.

```python
# todos.py
...

user_dependency = Annotated[dict, Depends(get_current_user)]

...

@router.get("/todos")
async def read_all(user: user_dependency, db: db_dependency):
#                  ^^^^^^^^^^^^^^^^^^^^^ get logged user information by injecting get_current_user dependency
    # if user is None, response 401 Unauthorized (even IDE shows this situation is unreachable...)
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized to view todos")

    return db.query(models.Todos).filter(models.Todos.owner_id == user.get("id")).all()
    #                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ instead of all todos, query only the todos of the logged user

...

```
