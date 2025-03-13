---
date: 2025-03-11
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Path Parameter

The path parameter corresponds to the dynamic path in the URL, and the client can pass any string.

Path parameters are usually used to specify the *location* of data.

```py
from fastapi import FastAPI


app = FastAPI()

@app.get("/items/{item_id}")
#          ^^^^^ ^^^^^^^^^ corresponding to the dynamic path of the URL, client can pass any string here
#          ^^^^^ corresponds to the static path of the URL, client must pass exactly this string, or this endpoint won't be matched
def read_item(item_id: int):
#             ^^^^^^^  ^^^ type declaration is required, even if it's a string. And FastAPI will convert it to the specified type automatically
#             ^^^^^^^ the variable which corresponds to the dynamic path of the URL will be passed as an parameter to the function
#             ^^^^^^^ so we call it `path parameter`

    return {"item_id": item_id}
    #                  ^^^^^^^ then, we can consume it in the function
```

So, when you go to `http://127.0.0.1:8000/items/42`, you will get a JSON response:

```json
{"item_id": 42}
```
