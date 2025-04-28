---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: define a field of request body to be optional

Sometimes, certain fields in the request body are optional. In this case, you can use `Optional[] = None` to define it.

```py
from typing import Optional # import
from fastapi import FastAPI
from pydantic import BaseModel


app = FastAPI()

class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int

    def __init__(self, id: int, title: str, author: str, description: str, rating: int):
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating


class BookRequest(BaseModel):
    title: str
    author: str
    description: Optional[str] = None
    #            ^^^^^^^^^^^^^^^^^^^^ then, this field is optional
    rating: int


BOOKS = [ ... ]

@app.get("/books")...

@app.post("/books")
def create_book(book_request: BookRequest):...
#                             ^^^^^^^^^^^

```


