---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: add explicit description to fields of request body by Field

In some cases, the schema content for the Request body field on Swagger UI may not be clear enough. In this case, you can add a more detailed description through the Field.

```py
class BookRequest(BaseModel):
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = Field(
        description="max_length=100",
        max_length=100,
        default=None
    )
    rating: int = Field(ge=1, le=5)
```

**Schema content on the Swagger UI:**
*BookRequest* object
title* string >= 3 characters
author* string >= 3 characters
description  (string | null)
rating* integer\[1, 5]

You can see that the schema of Swagger UI does not display the validation rule max_length=100 for description, so we can supplement this information through the description parameter of the Field.

```py
class BookRequest(BaseModel):
    title: str = Field(min_length=3)
    author: str = Field(min_length=3)
    description: Optional[str] = Field(
        description="max_length=100", # Add a more detailed description
        max_length=100,
        default=None
    )
    rating: int = Field(ge=1, le=5)
```


**Schema content on the Swagger UI:**
*BookRequest* object
title* string >= 3 characters
author* string >= 3 characters
description  (string | null) max_length=100
rating* integer\[1, 5]



