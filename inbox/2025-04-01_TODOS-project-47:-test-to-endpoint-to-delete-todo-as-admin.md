---
date: 2025-04-01
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 47: test to endpoint to delete todo as admin

Before we start, let's check the endpoint to delete a todo as an admin.

```py
# TodoApp/routers/admin.py

...

@router.delete("/todos/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_todo(user: user_dependency, db: db_dependency, todo_id: int = Path(gt=0)):
    if user is None or user.get("role") != "admin":
        raise HTTPException(status_code=401, detail="Unauthorized")
    
    todo = db.query(Todos).filter(Todos.id == todo_id).first()
    if todo is None:
        raise HTTPException(status_code=404, detail="Todo not found")
    
    db.delete(todo)
    db.commit()
```

Then, let's write a test for this endpoint.

```py
...

def test_delete_todo(test_todo):
    response = client.delete("/admin/todos/1")
    assert response.status_code == status.HTTP_204_NO_CONTENT

    db = TestingSessionLocal()
    model = db.query(Todos).filter(Todos.id == 1).first()
    assert model is None

# further test the edge case when the todo is not found
def test_delete_todo_not_found(test_todo):
    response = client.delete("/admin/todos/999")
    #                                      ^^^ todo id that does not exist
    assert response.status_code == status.HTTP_404_NOT_FOUND
    assert response.json() == {"detail": "Todo not found"}
