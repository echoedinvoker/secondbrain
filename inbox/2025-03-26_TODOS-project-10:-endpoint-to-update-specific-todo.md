---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 10: endpoint to update specific todo

```py
...

@app.post("/todos")
async def create_todo(db: db_dependency,new_todo: TodoRequest):
    todo = models.Todos(**new_todo.model_dump())
    db.add(todo)
    db.commit()

# create a new endpoint to update a specific todo
@app.put("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
#                 ^^^^^^^ dynamic path to pick specific todo by id
async def update_todo(db: db_dependency,
                      new_todo: TodoRequest, # updated todo data 
                      todo_id: int = Path(gt=0)): # id of todo to update, Path parameter, and use `Path` to validate the id

    # query the todo by id
    todo = db.query(models.Todos).filter(models.Todos.id == todo_id).first()

    # if todo is not found, raise 404 error
    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    # update the queried todo with new data (MUST mutate the QURIED todo object instead of creating a new one)
    todo.title = new_todo.title
    todo.description = new_todo.description
    todo.priority = new_todo.priority
    todo.completed = new_todo.completed

    # same as create_todo, commit the changes
    db.add(todo)
    db.commit()
```

You will find that both `create_todo` and `update_todo` only use `db.add(todo)` and `db.commit()` to add or update data in the database. This is because SQLAlchemy will automatically check whether the `todo` object is queried or newly created, and automatically determine whether to perform `INSERT` or `UPDATE` operations.

So, you should make sure that the `todo` object you are updating is queried from the database, and then mutate the queried object to update the data. If you create a new `Todo` object and try to update it, SQLAlchemy will treat it as a new object and perform an `INSERT` operation instead of an `UPDATE` operation.

