---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: add publish year to books and make it a filter when query books

## Add publish year to books

```py
from typing import Optional
from fastapi import FastAPI
from pydantic import BaseModel, Field


app = FastAPI()

class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int
    publish_year: int # Add publish year to Book class

    def __init__(self, title: str, author: str, description: str, rating: int, publish_year: int, id: int=0):
    #                                                                          ^^^^^^^^^^^^^^^^^ Add publish_year to __init__
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating
        self.publish_year = publish_year # Add publish year to __init__


class BookRequest(BaseModel):
    id: Optional[int] = Field(title='not required for create', default=None)
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = Field(
        description="max_length=100",
        max_length=100,
        default=None
    )
    rating: int = Field(ge=1, le=5)
    publish_year: int = Field(ge=1900, le=2031) # add publish_year to BookRequest and set the range of publish_year

    model_config = {
        "json_schema_extra": {
            "example": {
                "title": "哈利波特與魔法石",
                "author": "J.K. 羅琳",
                "rating": 4,
                "publish_year": 1993 # Add publish_year to example
            }
        }
    }


BOOKS = [
    Book(
        id=1,
        title="哈利波特與魔法石",
        author="J.K. 羅琳",
        description="一個住在樓梯下儲藏室的男孩，發現自己是一個巫師，並開始了他在霍格華茲魔法學校的冒險。",
        rating=5,
        publish_year=1997 # Add publish_year to book dummy data
    ),
    ...
]

# check all endpoints if any of them need to be modified because of the new field
@app.get("/books")
def read_all_books():
    return BOOKS

@app.get("/books/{book_id}")
def read_book(book_id: int):
    for book in BOOKS:
        if book.id == book_id:
            return book

@app.get("/books/")
def read_books_by_rating(rating: int):
    return [book for book in BOOKS if book.rating == rating]

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
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book.id:
            BOOKS[i] = Book(**book.model_dump())

@app.delete("/books/{book_id}")
def delete_book(book_id: int):
    for i in range(len(BOOKS)):
        if BOOKS[i].id == book_id:
            BOOKS.pop(i)
            break

# ... there is no endpoint that needs to be modified :D

```

## Make publish year a filter when query books

We already have an endpoint that queries all books and another endpoint that queries books by rating. Follow this pattern, we can add another endpoint that queries books by publish year.

But I think it's better to combine all the filters into the first endpoint `/books` and use query parameters to filter books with different criteria.

```py
# combine all filters into the first endpoint
@app.get("/books")
def read_books(rating: Optional[int] = None, year: Optional[int] = None):
#              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ two query parameters, and they are all optional 
    restuls = BOOKS
    if rating: # if rating is provided, filter books by rating
        restuls = [book for book in restuls if book.rating == rating]
    if year: # if year is provided, filter books by year
        restuls = [book for book in restuls if book.publish_year == year]
    return restuls # return the filtered results

@app.get("/books/{book_id}") # path parameter is not counted as a filter, so it's still a separate endpoint
def read_book(book_id: int):
    for book in BOOKS:
        if book.id == book_id:
            return book

# @app.get("/books/") # this endpoint is not needed anymore because the filter is combined into the first endpoint
# def read_books_by_rating(rating: int):
#     return [book for book in BOOKS if book.rating == rating]

...
