---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 26: Authenticate GET request to get specific todo by id

In [[2025-03-26_TODOS-project-8:-endpoint-to-query-specific-todo-with-id|TODOS project 8: endpoint to query specific todo with id]], we've created an endpoint to query a specific todo by its id but we didn't add any authentication to it. Now, we want to add authentication to this endpoint and only allow the todo which belongs to the logged-in user to be queried.

```py
# todos.py
...

user_dependency = Annotated[dict, Depends(get_current_user)]

...

@router.get("/todos/{todo_id}")
async def read_todo(user: user_dependency, db: db_dependency, todo_id: int):
#                   ^^^^^^^^^^^^^^^^^^^^^ inject get_current_user dependency to get logged-in user information

    # if user is None, response 401 Unauthorized
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")

    todo = db.query(models.Todos).filter(models.Todos.id == todo_id)\
            .filter(models.Todos.owner_id == user.get("id")).first()
    #       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add one more filter to check if the todo belongs to the logged-in user

    if todo is not None:
        return todo

    raise HTTPException(status_code=404, detail="Todo not found")

...

```
