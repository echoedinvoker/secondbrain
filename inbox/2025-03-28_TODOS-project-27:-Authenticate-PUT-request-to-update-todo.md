---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 27: Authenticate PUT request to update todo

In [[2025-03-26_TODOS-project-10:-endpoint-to-update-specific-todo|TODOS project 10: endpoint to update specific todo]], we've created an endpoint to update a specific todo. However, we didn't add any authentication to it. Now, we want to add authentication to this endpoint and only allow to update a todo belonging to the logged-in user.

```py
# todos.py
...

user_dependency = Annotated[dict, Depends(get_current_user)]

class TodoRequest(BaseModel):
    title: str = Field(min_length=3)
    description: str = Field(min_length=3, max_length=100)
    priority: int = Field(ge=1, le=5)
    completed: bool

...

@router.put("/todos/{todo_id}")
async def update_todo(user: user_dependency,
#                     ^^^^^^^^^^^^^^^^^^^^^^ get logged-in user information by injecting the dependency `get_current_user` and authenticate the request
                      db: db_dependency,
                      new_todo: TodoRequest,
                      todo_id: int = Path(gt=0)):

    # if user is None, respond with 401 Unauthorized
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")

    todo = db.query(models.Todos).filter(models.Todos.id == todo_id)\
            .filter(models.Todos.owner_id == user.get("id")).first()
    #       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ add one more filter to check if the todo belongs to the logged-in user

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    setattr(todo, "title", new_todo.title)
    setattr(todo, "description", new_todo.description)
    setattr(todo, "priority", new_todo.priority)
    setattr(todo, "completed", new_todo.completed)

    db.add(todo)
    db.commit()

@router.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(db: db_dependency, todo_id: int = Path(gt=0)):
    query = db.query(models.Todos).filter(models.Todos.id == todo_id)
    todo = query.first()

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    query.delete()
    db.commit()
```
