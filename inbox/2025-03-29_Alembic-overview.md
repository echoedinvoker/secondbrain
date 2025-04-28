---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Alembic overview

## What is Alembic?

- Lightweight database *migration tool for SQLAlchemy*

- Migration tools allow us to plan, transfer and upgrade resources within databases

- It allows us to *modify our database schema after it has been created*

- SQLAlchemy itself will only create new databases tables for us, not enhance any.


## How does Alembic work?

- Alembic provides the *creation and invocation of change management scripts*

- This allows us to be able to create migration environments and be able to change database schemas in a controlled manner


## Why we need Alembic?

- As our application evolves, our database will need to evolve as well

- Alembic helps us be able to keep modifying our database to keep up with rapid development requirements

- We will be using Alembic on tables that *already have data*. This allow us to be able to continually create additional content that works within our application!

