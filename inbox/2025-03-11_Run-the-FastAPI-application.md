---
date: 2025-03-11
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Run the FastAPI application


## Using Uvicorn

```sh
$ uvicorn main:app --reload
# ^^^^^^^ ^^^^ ^^^ ^^^^^^^^ means it'll reload the server whenever there is a change in the code
# ^^^^^^^ ^^^^ ^^^ variable that holds the FastAPI instance
# ^^^^^^^ ^^^^ file that contains the FastAPI instance
# ^^^^^^^ start the server

INFO:     Will watch for changes in these directories: ['/home/matt/Projects/fastapi']
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)  # This is the URL where the server is running
INFO:     Started reloader process [4235] using WatchFiles
INFO:     Started server process [4237]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

## Using FastAPI CLI

You need to install FastAPI with the `standard` extra to use the CLI.

```sh
$ pip install "fastapi[standard]"
```

Then you can run the FastAPI application using the CLI.

```sh
$ fastapi run main.py 
#         ^^^ ^^^^^^^ the file that contains the FastAPI instance
#         ^^^ start the server in production mode

   FastAPI   Starting production server 🚀
 
             Searching for package file structure from directories with 
             __init__.py files
             Importing from /home/matt/Projects/fastapi
 
    module   🐍 books.py
 
      code   Importing the FastAPI app object from the module with the following
             code:
 
             from books import app
 
       app   Using import string: books:app
 
    server   Server started at http://0.0.0.0:8000
    server   Documentation at http://0.0.0.0:8000/docs
 
             Logs:
 
      INFO   Started server process [6893]
      INFO   Waiting for application startup.
      INFO   Application startup complete.
      INFO   Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

Or you can run the FastAPI application in development mode.

```sh
$ fastapi dev main.py 
#         ^^^ start the server in development mode

   FastAPI   Starting development server 🚀
 
             Searching for package file structure from directories with 
             __init__.py files
             Importing from /home/matt/Projects/fastapi
 
    module   🐍 books.py
 
      code   Importing the FastAPI app object from the module with the following
             code:
 
             from books import app
 
       app   Using import string: books:app
 
    server   Server started at http://127.0.0.1:8000
    server   Documentation at http://127.0.0.1:8000/docs
 
       tip   Running in development mode, for production use: fastapi run
 
             Logs:
 
      INFO   Will watch for changes in these directories: 
             ['/home/matt/Projects/fastapi']
      INFO   Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
      INFO   Started reloader process [7042] using WatchFiles
      INFO   Started server process [7044]
      INFO   Waiting for application startup.
      INFO   Application startup complete.
```

In fact, FastAPI also uses uvicorn to start the FastAPI application, but FastAPI will handle some settings for you, allowing you to directly use the fastapi command to start the FastAPI application.



