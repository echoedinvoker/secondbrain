---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Set prisma with sqlite to our project

```bash
mattc@x ~/reposities> npm install prisma  # Install prisma, which is a cli tool to interact with the database
added 6 packages in 7s

mattc@x ~/reposities [1]> npx prisma init --datasource-provider sqlite # Initialize prisma with sqlite db
✔ Your Prisma schema was created at prisma/schema.prisma
  You can now open it in your favorite editor.
Next steps:
1. Set the DATABASE_URL in the .env file to point to your existing database. If your database has no tables yet, read https://pris.ly/d/getting-started
2. Run prisma db pull to turn your database schema into a Prisma schema.
3. Run prisma generate to generate the Prisma Client. You can then start querying your database.
4. Tip: Explore how you can extend the ORM with scalable connection pooling, global caching, and real-time database events. Read: https://pris.ly/cli/beyond-orm
More information in our documentation:
https://pris.ly/d/getting-started

```

Then the default `prisma/schema.prisma` file will be created with the following content:
```prisma
// prisma/schema.prisma
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}
```

You can [[2024-10-13_Adding-model-to-prisma-schema|add models]] to the schema file.
