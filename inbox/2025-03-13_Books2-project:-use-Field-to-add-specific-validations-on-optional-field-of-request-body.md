---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: use Field to add specific validations on optional field of request body

Sometimes, specific validation is also needed for optional fields in the request body when provided by the client. In such cases, we can still use `Field` in conjunction with `Optional` to achieve this purpose.

```py
...

app = FastAPI()

class Book:...

class BookRequest(BaseModel):
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = None # if we want to add specific validations on this field if it is present
    rating: int = Field(ge=1, le=5)

...

```

Replace `None` with `Field` to add specific validations on the optional field `description` of the request body. But still need to define the default value of the field as `None` in the `Field`.

```py
...

app = FastAPI()

class Book:...

class BookRequest(BaseModel):
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = Field(max_length=100, default=None)
    #                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    rating: int = Field(ge=1, le=5)

...

```
