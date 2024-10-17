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

prisma.schema not only defines the database itself, but also the models or tables in the database are defined here.

We add a simple model `Snippet` with a primary key `id`, which is an autoincrementing integer, and two fields `title` and `content` which are strings as shown below:

```prisma
model Snippet {
  id        Int       @id @default(autoincrement())
  title     String
  content   String
}

```
After adding the model to the prisma schema, don't forget to [[2024-10-16_Commit-prisma-schema|commit]] it.
