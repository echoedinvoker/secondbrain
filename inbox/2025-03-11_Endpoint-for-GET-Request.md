---
date: 2025-03-11
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Endpoint for GET Request

## First API endpoint for GET request

```py
    from fastapi import FastAPI


    app = FastAPI() # Creating an instance of FastAPI class

    @app.get("/api-endpoint") # This is a decorator from FastAPI, which return the below function's output to the client
#        ^^^ ^^^^^^^^^^^^^^^ endpoint, which equals to the URL path
#        ^^^ This means that this endpoint will only accept GET requests from the client
    async def first_api(): # 
#   ^^^^^ this is not necessary, FastAPI will automatically check if the function is async or not
#         if it is async, it will run it asynchronously
        return {"message": "Hello World"}
#       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ function's output will be returned to the client

```

![use-browser-send-get.png](../assets/imgs/use-browser-send-get.png)


