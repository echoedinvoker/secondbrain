---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Endpoint for DELETE Request

In FastAPI, you can define a endpoint that handles `DELETE` requests using the `@app.delete` decorator. And you can define path parameters in the URL path that will be consumed by the function to delete the specific item.

```python
@app.delete("/items/{item_id}")
#                   ^^^^^^^^^ dynamic path of the URL
def delete_item(item_id: int):
#               ^^^^^^^^^^^^ Path parameter corresponding to the dynamic path
    return {"item_id": item_id}
    #                  ^^^^^^^ consumed path parameter in the function
```

