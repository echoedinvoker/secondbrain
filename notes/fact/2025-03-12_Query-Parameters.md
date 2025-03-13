---
date: 2025-03-12
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Query Parameters

The query parameter corresponds to the key-value pair after the `?` in the URL, for example `?key1=value1&key2=value2`, which is also called the *query string*.

Query parameters are used to *filter* the data that is returned from the server. 

```py
# Request URL: http://example.com/items/?limit=10&offset=20

@app.route('/items/') # query parameters are not part of the route
def get_items(limit: int, offset: int):
#             ^^^^^^^^^^^^^^^^^^^^^^^ query parameters are passed as function arguments, with the same name as the key
#                                     and the type MUST be specified, even if it's a string
    return f"Items from {offset} to {offset + limit}"
    #                    ^^^^^^      ^^^^^^   ^^^^^ we can consume the query parameters in the function
```

And we can also use it with path parameters:

```py
# Request URL: http://example.com/items/10/?limit=10&offset=20

@app.route('/items/{item_id}/') # path parameters are part of the route, query parameters are not
def get_item(item_id: int, limit: int, offset: int):
#            ^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^ parameters which aren't part of the path are query parameters
#            ^^^^^^^^^^^^ parameter which is part of the path is a path parameter
    return f"Item {item_id} from {offset} to {offset + limit}"
    #              ^^^^^^^        ^^^^^^      ^^^^^^   ^^^^^ we can consume path parameters and query parameters in the function
```

