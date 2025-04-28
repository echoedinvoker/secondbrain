---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: update book with PUT request
```py
class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int

    def __init__(self, title: str, author: str, description: str, rating: int, id: int=0):
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating


class BookRequest(BaseModel):
    id: Optional[int] = Field(title='not required for create', default=None) # add id field, it's optional because it's not required for create
                                                                             # but for update, it's required for finding the book in this case
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = Field(
        description="max_length=100",
        max_length=100,
        default=None
    )
    rating: int = Field(ge=1, le=5)

...

# create an endpoint to update a book
@app.put("/books")
#   ^^^^ means accepting PUT requests
def update_book(book: BookRequest):
#                     ^^^^^^^^^^^ use Pydantic model for request body
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book.id: # find the book by id
            BOOKS[i] = Book(**book.model_dump()) # update the book

```

Test with Swagger UI, you can use request body as:
{
  "id": 1,
  "author": "余華",
  "rating": 4,
  "title": "活著"
}
It'll update the book with id 1, and return 200 and null

But if you use request body as:
{
  "id": 100, <--------------- id which doesn't exist
  "author": "余華",
  "rating": 4,
  "title": "活著",
}

It still returns 200 and null even though the book with id 100 doesn't exist. We should add some error handling for this case. We'll talk about it in the next section.

