---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: add validation to query parameters

This is very similar to [[2025-03-25_Books2-project:-add-validation-to-path-parameters|Books2 project: add validation to path parameters]], but this time we are going to validate query parameters, and we are going to use the `query` method to do so.

```py
...
from fastapi import FastAPI, Path, Query
#                                  ^^^^^

...

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
    #             ^^^^^^^^^^^^^^^^^
    publish_year: int = Field(ge=1900, le=2031)
    #                   ^^^^^^^^^^^^^^^^^^^^^^^

    ...

...

@app.get("/books")
def read_books(
    rating: Optional[int] = Query(default=None, ge=1, le=5),
    #                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use Query to add validation to query parameters, default=None is for OPTIONAL
    year: Optional[int] = Query(default=None, ge=1900, le=2031)
    #                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use Query to add validation to query parameters, default=None is for OPTIONAL
):
    restuls = BOOKS
    if rating:
        restuls = [book for book in restuls if book.rating == rating]
    if year:
        restuls = [book for book in restuls if book.publish_year == year]
    return restuls

...

```

Now, if you try to use a query parameter that is not valid, you will get an error message and a 422 status code.

```json
{
    "detail": [
        {
            "loc": [
                "query",
                "rating"
            ],
            "msg": "ensure this value is less than or equal to 5",
            "type": "value_error.number.not_le",
            "ctx": {
                "limit_value": 5
            }
        }
    ]
}
```

