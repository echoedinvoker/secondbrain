---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Example: DELETE Request to delete item

```py
@app.delete("/books/{book_title}")
def delete_book(book_title: str):
    for i in range(len(BOOKS)):
        title = BOOKS[i].get("title")
        if title is not None and title.casefold() == book_title.casefold():
            BOOKS.pop(i)
            break
```
