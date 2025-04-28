---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: delete a book by id

```py
...

@app.delete("/books/{book_id}")
#                   ^^^^^^^^^ dynamical path
def delete_book(book_id: int):
#               ^^^^^^^^^^^^ match the dynamical path, so this is a path parameter, and type is int (MUST explicitly specify the type)
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id: # find the book by id
            BOOKS.pop(i) # delete the book from the list by .pop() method
            break # break the loop
```

Test it on the Swagger UI, if successful, you will see the response status code is 200 and response body is null.


