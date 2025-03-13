---
date: 2025-03-12
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Book project: endpoint to update data with the Body

## Scenario

Data:
```py
BOOKS = [
    {"title": "Harry Potter", "author": "J.K. Rowling", "category": "fantasy"},
    {"title": "Harry Potter 2", "author": "J.K. Rowling", "category": "fantasy"},
    {"title": "The Lord of the Rings", "author": "J.R.R. Tolkien", "category": "fantasy"},
    {"title": "The Lord of the Rings 2", "author": "J.R.R. Tolkien", "category": "fantasy"},
    {"title": "The Da Vinci Code", "author": "Dan Brown", "category": "thriller"},
    {"title": "The Da Vinci Code 2", "author": "Dan Brown", "category": "thriller"},
    {"title": "The Alchemist", "author": "Paulo Coelho", "category": "adventure"},
    {"title": "The Alchemist 2", "author": "Paulo Coelho", "category": "adventure"},
]
```
Assuming we need an endpoint that allows us to select a book by its title and update the book's information.


## Implementation
```py
from fastapi import Body, FastAPI

app = FastAPI()

@app.put("/books")
def update_book(book=Body()):
#               ^^^^^^^^^^^ accept the request body as a parameter 'book'
    # consume the request body and update the book with the same title
    for i in range(len(BOOKS)):
        book_title = BOOKS[i].get("title")
        if book_title is not None and book_title.casefold() == book.get("title").casefold():
            BOOKS[i] = book
