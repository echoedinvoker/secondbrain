---
date: 2025-03-12
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Endpoint for POST Request

- Used to create data
- POST can have a *body* that has additional information that GET does not have

```py
@app.post("/items")
#   ^^^^^  ^^^^^^ in convention, the endpoint is named by the resource it is creating, not the action
#   ^^^^^ means that this endpoint is for POST requests which have body for additional information
def create(item=Body()):
#          ^^^^^^^^^^^ accepts the body of the request and assigns it to the variable `item`
    return item
    #      ^^^^ then, we can consume the body in the function
```

