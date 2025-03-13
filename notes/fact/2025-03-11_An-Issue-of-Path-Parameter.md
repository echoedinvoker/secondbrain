---
date: 2025-03-11
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# An Issue of Path Parameter

```py
from fastapi import FastAPI


app = FastAPI()

@app.get("/items/{item_id}")
#                ^^^^^^^ corresponding to the dynamic path of URL
def read_item(item_id: int):
    return {"item_id": item_id}

# a endpoint that never be matched because it always be matched by the previous one
@app.get("/items/enabled")
#                ^^^^^^^ corresponding to the static path of URL
def read_enabled():
    return {"enabled": True}
```

In the above code, the second endpoint `/items/enabled` will never be matched because even if the URL path is `/items/enabled`, the path `enabled` will be treated as a dynamic path and will be matched by the first endpoint `/items/{item_id}`. To fix this issue, you can change the order of the endpoints.


```py
from fastapi import FastAPI


app = FastAPI()

# move the endpoint with static path to the top
@app.get("/items/enabled")
def read_enabled():
    return {"enabled": True}

# move the endpoint with dynamic path to the bottom
@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}

```
