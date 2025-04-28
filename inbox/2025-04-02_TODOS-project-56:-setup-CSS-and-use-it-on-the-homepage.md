---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 56: setup CSS and use it on the homepage

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
    ├── static # new folder for static information like static CSS and JS files
    │   └── css # new folder for CSS files
    │       └── base.css # new CSS file
    ├── templates
    │   └── home.html
    └── test
```

```css
h1 {
  color: red;
}
```

```py
# main.py
...
from fastapi.staticfiles import StaticFiles # used to load static files by targetting the static folder


app = FastAPI()

...

# mount the static folder to the app
app.mount("/static", StaticFiles(directory="TodoApp/static"), name="static")
#          ^^^^^^^   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^

...

```

```html
<!DOCTYPE>
<html lang="en">
  <head>

    <!--add link to load the CSS file-->
    <link rel="stylesheet" type="text/css"
      href="{{ url_for('static', path='/css/base.css') }}">

    <meta charset="UTF-8">
    <title>TodoApp</title>
  </head>
  <body>
    <h1>Welcome to this FastAPI Course!</h1>
  </body>
</html>
```

Launched the app and the CSS is applied to the `h1` tag.


