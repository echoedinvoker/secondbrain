---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: add specific validations to request body by Field

In addition to basic validation of property and type, we can also use `Field` to add more validation conditions, such as `min_length`, `max_length`, `ge`, `le`, and so on.

```py
from fastapi import FastAPI
from pydantic import BaseModel, Field


app = FastAPI()

class Book: ...


class BookRequest(BaseModel):
    id: int
    title: str = Field(min_length=3)
    #         ^^^^^^^^^^^^^^^^^^^^^^
    author: str = Field(min_length=3)
    #          ^^^^^^^^^^^^^^^^^^^^^^
    description: str = Field(max_length=100)
    #               ^^^^^^^^^^^^^^^^^^^^^^^^
    rating: int = Field(ge=1, le=5)
    #          ^^^^^^^^^^^^^^^^^^^^


BOOKS = [ ... ]

@app.get("/books") ...

@app.post("/books")
def create_book(book_request: BookRequest):
#                             ^^^^^^^^^^^ validations are applied here, if any of them fails,
#                                         it will raise a validation error before reaching any codes inside the function
    book = Book(**book_request.model_dump())
    BOOKS.append(book)
    
```
