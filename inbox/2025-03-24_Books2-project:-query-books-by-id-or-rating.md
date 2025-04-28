---
date: 2025-03-24
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: query books by id or rating

## Query books by id

We want to obtain a book with a specific id, path parameter is suitable for this purpose.

```python
...

@app.get("/books/{book_id}")
def read_book(book_id: int):
    for book in BOOKS:
        if book.id == book_id:
            return book

# if book with id is not found, return 200 and response body is null
...

```

## Query books by rating

We want to obtain books with a specific rating, which can be filtered using query parameters.

```python
```py
@app.get("/books")
def read_all_books():
    return BOOKS

@app.get("/books/{book_id}")
def read_books_by_rating(book_id: int):
    for book in BOOKS:
        if book.id == book_id:
            return book

# create a new endpoint to filter books by rating
@app.get("/books/")
#         ^^^^^^^ which is different from "/books", so there is no conflict
def read_books_by_rating(rating: int):
#                        ^^^^^^^^^^^ not in the path, so this is a query parameter, not conflict to the previous one
    return [book for book in BOOKS if book.rating == rating]
```

Special attention should be paid to the order of routes and conflict issues.
