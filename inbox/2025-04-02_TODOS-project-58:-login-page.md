---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 58: login page

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
    ├── static
    │   ├── css
    │   │   ├── base.css
    │   │   └── bootstrap.css
    │   └── js
    │       ├── base.js
    │       ├── bootstrap.js
    │       ├── bootstrap.js.map
    │       ├── jquery-slim.js
    │       ├── popper.js
    │       └── popper.min.js.map
    ├── templates
    │   ├── home.html
    │   └── login.html # new file for login page
    └── test
```

```html
<!DOCTYPE>
<html lang="en">
  <head>
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/base.css') }}">
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/bootstrap.css') }}">
    <meta charset="UTF-8">
    <title>TodoApp</title>
  </head>
  <body>
    <div class="container">
      <div class="card">
        <div class="card-header">
          Login
        </div>
        <div class="card-body">
          <form id="loginForm">
            <div class="form-group">
              <label>Username</label>
              <input type="text" class="form-control" name="username" autocomplete="username" required>
            </div>
            <div class="form-group">
              <label>Password</label>
              <input type="password" class="form-control" name="password" autocomplete="current-password" required>
            </div>
            <button type="submit" class="btn btn-primary">Login</button>
          </form>
        </div>
        <div class="card-footer text-muted">
          <a href="/auth/register-page">Register?</a>
        </div>
      </div>
    </div>
  </body>
</html>
```

```py
...
from fastapi import APIRouter, Depends, HTTPException, Request
#                                                      ^^^^^^^ to provide the HTTP request information to the Jinja2
from fastapi.templating import Jinja2Templates # to load and render templates

...

db_dependency = Annotated[Session, Depends(get_db)]

templates = Jinja2Templates(directory="TodoApp/templates") # load the templates


### Pages ###  <-------- split the pages and endpoints codes, to make it more readable

# create a new router to render login page
@router.get("/login-page")
def render_login_page(request: Request):
#                     ^^^^^^^^^^^^^^^^ automatically injected according to its type (Request)
    return templates.TemplateResponse("login.html", {"request": request}) # render the login page



### Endpoints ###

...

```

Launch the server and test the login page http://localhost:8000/auth/login-page

We can see the login page with the form to input the username and password. But the form doesn't do anything yet. All this will be fixed once we implement our JavaScript, which will be implemented in the next topic.

