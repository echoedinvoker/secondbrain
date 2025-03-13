---
date: 2025-03-11
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Example: endpoint to read data with Path Parameter

```py
from fastapi import FastAPI


app = FastAPI()

BOOKS = [
    {"title": "Harry Potter", "author": "J.K. Rowling", "category": "fantasy"},
    {"title": "The Lord of the Rings", "author": "J.R.R. Tolkien", "category": "fantasy"},
    {"title": "The Da Vinci Code", "author": "Dan Brown", "category": "thriller"},
    {"title": "The Alchemist", "author": "Paulo Coelho", "category": "adventure"},
#    ^^^^^^^^^^^^^^^^^^^^^^^^ we want create a GET endpoint to query the specific book by title
]

@app.get("/books/{book_title}")
#                 ^^^^^^^^^^ Path Parameter
def read_book(book_title: str):
    for book in BOOKS:
        title = book.get("title")
        if title is not None and title.casefold() == book_title.casefold():
        #                                            ^^^^^^^^^^ ^^^^^^^^ stronger .lower() method to remove case sensitivity
        #                                            ^^^^^^^^^^ consume the path parameter to get the specific book 
            return book

```

So, if you run the application and go to `http://127.0.0.1:8000/books/The%20Alchemist`, you will get the following response:
(*%20* is the URL encoding for a space because URLs cannot have spaces)

```json
{
    "title": "The Alchemist",
    "author": "Paulo Coelho",
    "category": "adventure"
}
```
