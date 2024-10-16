---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Create prisma client to access database

```ts
import { PrismaClient } from "@prisma/client";

export const db = new PrismaClient()
```

Then we can use `db` to interact with the database.

If our prisma.schema has `Snippet` model, we can use `db` to do CRUD operations on it.
for example:

```ts
db.snippet.create({   // `snippet` must be all lowercase even we defined it as `Snippet` in prisma.schema
  data: {
    title: "My first snippet",
    content: "console.log('Hello world!')",
  },
});
```
