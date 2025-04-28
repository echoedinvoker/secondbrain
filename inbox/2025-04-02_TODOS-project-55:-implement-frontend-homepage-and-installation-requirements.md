---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 55: implement frontend homepage and installation requirements

First, we need to create a new folder called `templates` inside the `TodoApp` folder. This folder will contain all the HTML files for the frontend.

```sh
 tree
.
├── fastapienv
├── testdb.db
└── TodoApp
    ├── __init__.py
    ├── alembic
    ├── alembic.ini
    ├── database.py
    ├── main.py
    ├── models.py
    ├── routers
    ├── templates # new folder for HTML files
    │   └── home.html # HTML file for homepage
    └── test
```

Inside the `templates` folder, create a new file called `home.html` and add the following code:

```html
<!DOCTYPE>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>TodoApp</title>
  </head>
  <body>
    <h1>Welcome to this FastAPI Course!</h1>
  </body>
</html>
```

Next, we need to install the `aiofiles` and `jinja2` libraries to render the HTML templates in FastAPI.

```sh
 pip install aiofiles # for async file operations
 pip install jinja2 # for rendering HTML templates
```

Now, we need to load the templates folder in the FastAPI application and create a route for the homepage.

```py
# main.py
...
from fastapi.templating import Jinja2Templates


app = FastAPI()

...

templates = Jinja2Templates(directory="TodoApp/templates") # load the templates folder

# create a route for the homepage
@app.get("/")
def test():
    return templates.TemplateResponse("home.html") # render the home.html file
```

But above code doesn't work yet because Jinja2 template sometimes needs the HTTP request information to render the template.

```py
from fastapi import FastAPI, Request
#                            ^^^^^^^ type hint for HTTP request object

...

@app.get("/")
def test(request: Request):
#        ^^^^^^^^^^^^^^^^ request is a automatically injected according to its type, here we set its type to Request, so it will be injected with HTTP request information
    return templates.TemplateResponse("home.html", {"request": request})
    #                                              ^^^^^^^^^^^^^^^^^^^^ Jinja2 template sometimes needs the HTTP request information to render the template
    #                                                                   so we should always pass the request object to the template
                                                    

```
