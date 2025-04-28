---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Alembic installation, command, and files

## Installation
```bash
pip install alembic
```

## Command

After installing Alembic, we can use the following commands in the terminal.
```bash
alembic init "<folder name>" # Initialize a new, generic Alembic environment
alembic revision -m "<message>" # Create a new revision of the environment
alembic upgrade "<revision id>" # Run our upgrade migration to our database
alembic downgrade "<revision id>" # Run our downgrade migration to our database
```

## Files

*alembic.ini*
- File that alembic looks for when invoked
- Contains a bunch of configuration information for Alembic that we are able to change to match our project

*alembic directory*
- Has all environmental properties for alembic
- Holds all revisions of your application
- Where you can call the migrations for upgrading and downgrading
