---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 28: Authenticate DELETE request to delete specific todo

In [[2025-03-26_TODOS-project-11:-endpoint-to-delete-specific-todo|TODOS project 11: endpoint to delete specific todo]], we've created an endpoint to delete a specific todo. However, we didn't add any authentication to it. Now, we want to add authentication to this endpoint and only allow to delete a todo belonging to the logged-in user.

```py
# todos.py
...

user_dependency = Annotated[dict, Depends(get_current_user)]

...

@router.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(user: user_dependency, db: db_dependency, todo_id: int = Path(gt=0)):
#                     ^^^^^^^^^^^^^^^^^^^^^ get logged-in user information by injecting the dependency `get_current_user`

    # if user is None, reponse with 401 Unauthorized
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")
    
    query = db.query(models.Todos).filter(models.Todos.id == todo_id)\
            .filter(models.Todos.owner_id == user.get("id"))
    #       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add one more filter to check if the todo belongs to the logged-in user

    todo = query.first()

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    query.delete()
    db.commit()
```
