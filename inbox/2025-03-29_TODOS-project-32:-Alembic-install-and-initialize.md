---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 32: Alembic install and initialize

In order to update the database structure without deleting the database, and to be able to revert to a previous state and track changes in the database structure, we need to use the Alembic package to help us manage the database schema.

```sh
$pip install alembic
$cd TodoApp/
$alembic init alembic # initialize alembic
#             ^^^^^^^ set the name of the directory to store the migration files

  # Creating directory '/home/matt/Projects/fastapi/TodoApp/alembic' ...  done
  # Creating directory '/home/matt/Projects/fastapi/TodoApp/alembic/versions' ...  done
  # Generating /home/matt/Projects/fastapi/TodoApp/alembic/README ...  done
  # Generating /home/matt/Projects/fastapi/TodoApp/alembic.ini ...  done
  # Generating /home/matt/Projects/fastapi/TodoApp/alembic/env.py ...  done
  # Generating /home/matt/Projects/fastapi/TodoApp/alembic/script.py.mako ...  done
  # Please edit configuration/connection/logging settings in
  # '/home/matt/Projects/fastapi/TodoApp/alembic.ini' before proceeding.

 tree
.
├── __init__.py
├── alembic # alembic directory
│   ├── env.py # alembic environment configuration
│   ├── README
│   ├── script.py.mako # migration script template
│   └── versions # where the migration files will be stored
├── alembic.ini # alembic configuration file
├── database.py
├── main.py
├── models.py
└── routers
    ├── __init__.py
    ├── admin.py
    ├── auth.py
    └── todos.py
```
```ini
# alembic.ini
[alembic]
...

sqlalchemy.url = postgresql://postgres:1234@localhost:5432/todos
#                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ should be the same as the database URL in the database.py file

...

```

```py
# alembic/env.py
...

import models # import the models module

# if config.config_file_name is not None:  # remove the if statement
fileConfig(config.config_file_name)

target_metadata = models.Base.metadata
#                 ^^^^^^^^^^^^^^^^^^^^^^ set the target metadata to the metadata of the Base class in the models module

...

```
