---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: raise HTTP Exceptions when not found

`HTTPException` is a class that can be `raise`, it will return a response with the status code and detail you set. The common use case is to raise `HTTPException` with status code `404` when the requested data is not found.

```py
...

@app.get("/books/{book_id}")
def read_book(book_id: int = Path(gt=0)):
    for book in BOOKS:
        if book.id == book_id:
            return book

...

@app.put("/books")
def update_book(book: BookRequest):
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book.id:
            BOOKS[i] = Book(**book.model_dump())

@app.delete("/books/{book_id}")
def delete_book(book_id: int = Path(gt=0)):
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id:
            BOOKS.pop(i)
            break
```

Even if no book is found in the above three endpoints, they will still return `200 OK`, so the client cannot know if the data has really been deleted or updated.

```py
...

@app.get("/books/{book_id}")
def read_book(book_id: int = Path(gt=0)):
    for book in BOOKS:
        if book.id == book_id:
            return book
    # if not return book, raise HTTPException with status code 404 and detail "Book not found"
    raise HTTPException(status_code=404, detail="Book not found")


@app.put("/books")
def update_book(book: BookRequest):
    book_changed = False # create a variable to check if the book has been changed
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book.id:
            BOOKS[i] = Book(**book.model_dump())
            book_changed = True # if any book is changed, set book_changed to True

    # if no book is changed, raise HTTPException with status code 404 and detail "Book not found"
    if not book_changed:
        raise HTTPException(status_code=404, detail="Book not found")

@app.delete("/books/{book_id}")
def delete_book(book_id: int = Path(gt=0)):
    book_deleted = False # create a variable to check if the book has been deleted
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id:
            BOOKS.pop(i)
            book_deleted = True # if any book is deleted, set book_deleted to True
            break

    # if no book is deleted, raise HTTPException with status code 404 and detail "Book not found"
    if not book_deleted:
        raise HTTPException(status_code=404, detail="Book not found")

```

Now if no book is found in the above three endpoints, they will return `404 Not Found` with the detail "Book not found". So the client can know if the data has really been deleted or updated.

```json
{
  "detail": "Book not found"
}
```
