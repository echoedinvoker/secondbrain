---
date: 2025-03-26
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 11: endpoint to delete specific todo

```py
...

# create a new endpoint to delete a specific todo
@app.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
#                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ specify the status code of the response to 204
async def delete_todo(db: db_dependency, todo_id: int = Path(gt=0)):
    query = db.query(models.Todos).filter(models.Todos.id == todo_id) # not using .first() here directly, because we need to use `Query` object to delete the record
                                                                      # if use .first(), it'll turn the `Query` object into a `Todo` object (ORM model)

    todo = query.first() # get the first record from the query, if exists it'll return a `Todo` object, otherwise `None`

    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")

    query.delete() # use `Query` object to delete the record (prepare)
    db.commit() # commit the transaction to actually delete the record

```
The `Query` object has some methods related to the database, and there are also some methods that will convert the `Query` object into an ORM model object. In this case, the methods of the `Query` object cannot be used anymore, so be aware of this.
