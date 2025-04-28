---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: replace default example value of request body on Swagger UI with custom value

In the default example values of the request body in Swagger UI, some values are often simply entered as `"string"` or `1`, as shown below:

```json
{
  "title": "string",
  "author": "string",
  "description": "string",
  "rating": 1
}
```

We can customize these values using the Request body model of Pydandic.

```python
...

class BookRequest(BaseModel):
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = Field(
        description="max_length=100",
        max_length=100,
        default=None
    )
    rating: int = Field(ge=1, le=5)

    # Custom example value of request body
    model_config = {
        "json_schema_extra": {
            "example": {
                "title": "活著",
                "author": "余華",
                # we can even omit the optional field in this case 
                "rating": 4
            }
        }
    }


@app.post("/books")
def create_book(book_request: BookRequest): ...
#                             ^^^^^^^^^^^

```

In the above code, we have defined a custom example value for the request body model `BookRequest`. This custom example value will be displayed in the Swagger UI instead of the default values.

```json
{
  "author": "余華",
  "rating": 4,
  "title": "活著"
}
```


