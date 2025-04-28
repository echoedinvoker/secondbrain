---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: instead of user input id, generate id automatically

When adding a new book, users no longer need to input an id, as it will be automatically generated.

```py
from fastapi import FastAPI
from pydantic import BaseModel


app = FastAPI()

class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int

    def __init__(self, title: str, author: str, description: str, rating: int, id: int = 0):
    #                                                                        ^^^^^^^^^^^^^ set a default value for id, and it should be after the required fields
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating


class BookRequest(BaseModel):
    # id: int   # request body don't need to input id anymore, because it will be generated automatically
    title: str
    author: str
    description: str
    rating: int


BOOKS = [ ... ]

@app.get("/books")...

# creat a function to find the book id and return the book with it
def find_book_id(book: Book):
    book.id = 1 if not BOOKS else BOOKS[-1].id + 1
    return book

@app.post("/books")
def create_book(book_request: BookRequest):
    book = Book(**book_request.model_dump()) # because of we've set the default value for id, so even we don't provide it, it won't raise an error
    book = find_book_id(book) # refresh the book with the new id by above function
    BOOKS.append(book)
    
```

Try to add a new book:

```sh
curl -X 'POST' \
  'http://127.0.0.1:8000/books' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "title": "string",
  "author": "string",
  "description": "string",
  "rating": 0
}'
```

And check the books:

```sh
curl -X 'GET' \
  'http://127.0.0.1:8000/books' \
  -H 'accept: application/json'

[
  ...

  {
    "id": 5,
    "title": "活著",
    "author": "余華",
    "description": "講述了一個人在經歷各種苦難後，仍然堅強活下去的故事。",
    "rating": 4
  },
  {
    "id": 6, # the id is generated automatically
    "title": "string",
    "author": "string",
    "description": "string",
    "rating": 0
  }
]

```
