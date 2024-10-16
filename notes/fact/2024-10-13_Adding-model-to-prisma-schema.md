---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Adding model to prisma schema

**Adding a model to a Prisma schema**

We add a simple model `Snippet` with a primary key `id`, which is an autoincrementing integer, and two fields `title` and `content` which are strings as shown below:

```prisma
model Snippet {
  id        Int       @id @default(autoincrement())
  title     String
  content   String
}

```

**Let new schema work**

```bash
mattc@x ~/r/snippets (main)> npx prisma migrate dev # command to implement new schema
Environment variables loaded from .env
Prisma schema loaded from prisma/schema.prisma
Datasource "db": SQLite database "dev.db" at "file:./dev.db"

SQLite database dev.db created at file:./dev.db

✔ Enter a name for the new migration: … add model Snippet  # name of the migration `add model Snippet` in this case
Applying migration `20241013015629_add_model_snippet`

The following migration(s) have been created and applied from new schema changes:

migrations/
  └─ 20241013015629_add_model_snippet/
    └─ migration.sql

Your database is now in sync with your schema.

Running generate... (Use --skip-generate to skip the generators)

✔ Generated Prisma Client (v5.20.0) to ./node_modules/@prisma/client in 28ms
```
