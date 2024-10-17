---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Commit prisma schema

Every time you update the content of prisma.schema or create a new prism.schema file, you need to use the following command to commit it.

```bash
mattc@x ~/r/snippets (main)> npx prisma migrate dev # it will create/update `dev.db` file based on prism.schema

Environment variables loaded from .env
Prisma schema loaded from prisma/schema.prisma
Datasource "db": SQLite database "dev.db" at "file:./dev.db"

SQLite database dev.db created at file:./dev.db

✔ Enter a name for the new migration: … add model Snippet  # commit name
Applying migration `20241013015629_add_model_snippet`

The following migration(s) have been created and applied from new schema changes:

migrations/
  └─ 20241013015629_add_model_snippet/
    └─ migration.sql

Your database is now in sync with your schema.

Running generate... (Use --skip-generate to skip the generators)

✔ Generated Prisma Client (v5.20.0) to ./node_modules/@prisma/client in 28ms
```

`dev.db` is the file where the data is actually stored, prism.schema is just used to define it, and they are synchronized through commit.
