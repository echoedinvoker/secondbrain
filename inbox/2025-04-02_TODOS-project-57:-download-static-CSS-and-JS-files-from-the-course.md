---
date: 2025-04-02
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 57: download static CSS and JS files from the course

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
    ├── static # download the zip file and extract it here
    │   ├── css
    │   │   ├── base.css # lecturer wrote this
    │   │   └── bootstrap.css # bootstrap css
    │   └── js
    │       ├── base.js # lecturer wrote this, there are codes to send requests to the backend, we'll go through it in the following topics
    │       ├── bootstrap.js # bootstrap js
    │       ├── bootstrap.js.map
    │       ├── jquery-slim.js # attached to bootstrap js
    │       ├── popper.js # attached to bootstrap js
    │       └── popper.min.js.map
    ├── templates
    └── test

```
This course is about FastAPI, so we can directly download the CSS and JS files provided in the course instead of writing them from scratch.
And we won't talk about too much about the CSS and JS content, we'll focus on the FastAPI part.


