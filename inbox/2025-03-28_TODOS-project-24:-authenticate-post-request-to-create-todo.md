---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 24: authenticate post request to create todo

We previously created an API for creating todos in [[2025-03-26_TODOS-project-9:-endopint-to-add-new-todo-to-database|TODOS project 9: endopint to add new todo to database]], but this API is public and anyone can use it. Now we want to add authentication so that only logged-in users can use this API.

```py
# todos.py
...
from .auth import get_current_user # import get_current_user function from auth module

...
user_dependency = Annotated[dict, Depends(get_current_user)] # create a type that injects the `get_current_user` function as dependency

...

@router.post("/todos")
async def create_todo(user: user_dependency, db: db_dependency, new_todo: TodoRequest):
#                     ^^^^^^^^^^^^^^^^^^^^^ use dependency injection to get the current user

    # if user is None, return 401 Unauthorized
    # (but IDE shows this situation as unreachable because if user is None, it'll raise an exception
    # inside the `get_current_user` function)
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")

    todo = models.Todos(**new_todo.model_dump())
    db.add(todo)
    db.commit()

...

```

`get_current_user` also uses dependency injection to get the token from the request header and decode it to get the user information. You can check [[2025-03-28_TODOS-project-22:-Get-token-from-request-and-Decode-a-JWT|TODOS project 22: Get token from request and Decode a JWT]] to see how it works.

You can find that the dependency injection is very useful in FastAPI. It helps to keep the code clean and easy to read.

Let's add a new field `owner_id` to the ORM model `Todos` to store the user id who created the todo.

```py
# models.py
...

class Todos(Base):
    __tablename__ = 'todos'

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String)
    description = Column(String)
    priority = Column(Integer)
    completed = Column(Boolean, default=False)
    owner_id = Column(Integer) # add this field to show the owner of the todo
```

Now, we need to update the `create_todo` endpoint to set the `owner_id` field with the user id from the `get_current_user` function.

```py
...

@router.post("/todos", status_code=status.HTTP_201_CREATED)
#                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ set the status code to 201 Created
async def create_todo(user: user_dependency, db: db_dependency,new_todo: TodoRequest):
    if user is None:
        raise HTTPException(status_code=401, detail="Unauthorized")
    todo = models.Todos(**new_todo.model_dump(), owner_id=user.get("id"))
    #                                            ^^^^^^^^^^^^^^^^^^^^^^^
    #                                            set the owner_id field with the user id
    #                                            from the get_current_user function
    db.add(todo)
    db.commit()

...

```

Let's test the API on the Swagger UI:
```sh
curl -X 'POST' \
  'http://localhost:8000/todos' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "title": "learn fastapi",
  "description": "because it'\''s awesome!",
  "priority": 5,
  "completed": false
}'
```
You should get a 401 Unauthorized response because the request header doesn't contain any token. We can log in on the Swagger UI as below:

![click-lock-to-log-in.png](../assets/imgs/click-lock-to-log-in.png)

![log-in-success-on-Swagger.png](../assets/imgs/log-in-success-on-Swagger.png)

Then, you can try to create a new todo again.

```sh
curl -X 'POST' \
  'http://localhost:8000/todos' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJtYXR0IiwidXNlcl9pZCI6MSwiZXhwIjoxNzQzMTQ1ODAyfQ.HwMjmo6u6QModcc_-S3cK2AAbUULvwrnMlTFEpKmR94' \
  -H 'Content-Type: application/json' \
  -d '{
  "title": "learn fastapi",
  "description": "because it'\''s awesome!",
  "priority": 5,
  "completed": false
}'
```

You can see that there is a token bearer in the request header. So, you should get a 201 Created response now.

