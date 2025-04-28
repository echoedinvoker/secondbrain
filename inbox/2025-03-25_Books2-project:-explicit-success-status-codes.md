---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: explicit success status codes

```py
...

@app.get("/books")
def read_books(
    rating: Optional[int] = Query(default=None, ge=1, le=5),
    year: Optional[int] = Query(default=None, ge=1900, le=2031)
):
    restuls = BOOKS
    if rating:
        restuls = [book for book in restuls if book.rating == rating]
    if year:
        restuls = [book for book in restuls if book.publish_year == year]
    return restuls

...

@app.post("/books")
def create_book(book_request: BookRequest):
    book = Book(**book_request.model_dump())
    book = find_book_id(book)
    BOOKS.append(book)
    
def find_book_id(book: Book):
    book.id = 1 if not BOOKS else BOOKS[-1].id + 1
    return book

@app.put("/books")
def update_book(book: BookRequest):
    book_changed = False
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book.id:
            BOOKS[i] = Book(**book.model_dump())
            book_changed = True

    if not book_changed:
        raise HTTPException(status_code=404, detail="Book not found")

@app.delete("/books/{book_id}")
def delete_book(book_id: int = Path(gt=0)):
    book_deleted = False
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id:
            BOOKS.pop(i)
            book_deleted = True
            break

    if not book_deleted:
        raise HTTPException(status_code=404, detail="Book not found")

```

The four endpoints above will all return a status code of 200 when successful. We can explicitly specify the status code to differentiate successful responses for read, create, update, and delete.

```py
...
from starlette import status # fastapi is based on starlette, so we can import starlette directly

...

@app.get("/books")
def read_books(
    rating: Optional[int] = Query(default=None, ge=1, le=5),
    year: Optional[int] = Query(default=None, ge=1900, le=2031)
):
    restuls = BOOKS
    if rating:
        restuls = [book for book in restuls if book.rating == rating]
    if year:
        restuls = [book for book in restuls if book.publish_year == year]
    return restuls

...

@app.post("/books", status_code=status.HTTP_201_CREATED)
#                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ explicitly set status code to 201, which means created successfully
def create_book(book_request: BookRequest):
    book = Book(**book_request.model_dump())
    book = find_book_id(book)
    BOOKS.append(book)
    
def find_book_id(book: Book):
    book.id = 1 if not BOOKS else BOOKS[-1].id + 1
    return book

@app.put("/books", status_code=status.HTTP_204_NO_CONTENT)
#                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ explicitly set status code to 204, which means successful but no content and not created
def update_book(book: BookRequest):
    book_changed = False
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book.id:
            BOOKS[i] = Book(**book.model_dump())
            book_changed = True

    if not book_changed:
        raise HTTPException(status_code=404, detail="Book not found")

@app.delete("/books/{book_id}", status_code=status.HTTP_204_NO_CONTENT)
#                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ explicitly set status code to 204, which means successful but no content and not created
def delete_book(book_id: int = Path(gt=0)):
    book_deleted = False
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id:
            BOOKS.pop(i)
            book_deleted = True
            break

    if not book_deleted:
        raise HTTPException(status_code=404, detail="Book not found")
```
