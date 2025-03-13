---
date: 2025-03-11
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Book project: endpoint to read data

```python
from fastapi import FastAPI


app = FastAPI()

# a data about books
BOOKS = [
    {"title": "Harry Potter", "author": "J.K. Rowling", "category": "fantasy"},
    {"title": "The Lord of the Rings", "author": "J.R.R. Tolkien", "category": "fantasy"},
    {"title": "The Da Vinci Code", "author": "Dan Brown", "category": "thriller"},
    {"title": "The Alchemist", "author": "Paulo Coelho", "category": "adventure"},
]

@app.get("/books")
#    ^^^  ^^^^^^ endpoint name should reflect the resource name (convention)
#    ^^^ GET method to retrieve data
def read_all_books():
#   ^^^^^^^^^^^^^^ function name should reflect the action (convention)
    return BOOKS
#   ^^^^^^^^^^^^ return the data, with decorator, it will be converted to JSON and returned to the client


```

