---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: add validation to path parameters

In POST or PUT endpoints, we use Pydantic classes to validate request bodies, but in some GET endpoints, we use Path parameters to retrieve data. If these Path parameters also need validation, we can use `Path` to define them.

```python
...
from fastapi import FastAPI, Path
#                            ^^^^ this is used to validate path parameters

...

@app.get("/books/{book_id}")
def read_book(book_id: int = Path(gt=0)):
#                          ^^^^^^^^^^^^ add validation to path parameters, in this case, the book_id should be greater than 0
    for book in BOOKS:
        if book.id == book_id:
            return book

...

@app.delete("/books/{book_id}")
def delete_book(book_id: int = Path(gt=0)):
#                            ^^^^^^^^^^^^ without this validation, only type validation is performed
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id:
            BOOKS.pop(i)
            break
```

Then, if you quey `/books/0`, you will get a validation error with code *422*:

```json
{
  "detail": [
    {
      "loc": [
        "path",
        "book_id"
      ],
      "msg": "ensure this value is greater than 0",
      "type": "value_error.number.not_gt",
      "ctx": {
        "limit_value": 0
      }
    }
  ]
}
```
