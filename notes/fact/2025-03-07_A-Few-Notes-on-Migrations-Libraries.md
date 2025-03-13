---
date: 2025-03-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Few Notes on Migrations Libraries

In [[2025-03-07_Migration-Files|the previous topic]], we mentioned that migration files are not tied to any specific language. This means that in any language, we can find libraries to help us create/run migration files.

![libraries-migration-diff-lang.png](../assets/imgs/libraries-migration-diff-lang.png)


Let's check the introduction of `node-pg-migrate` on the npm website:

![up-migrate-node-pg-doc.png](../assets/imgs/up-migrate-node-pg-doc.png)

In the introduction page, we can see the codes of the example above. It writes the logic of creating the up part of the migration file into the `up` function and exports it.

However, the part of creating the database schema is not written in SQL script but in a declarative way. It'll be converted to SQL script by the library automatically.

> However, we do not recommend using declarative way to write the update schema logic, because each library will have some intrinc assumptions, such as columns defaulting to NULL or datetime defaulting to current timestamp, etc. These assumptions may lead to some unknown issues in your schema. So it is best to use raw SQL script to write the update schema logic.




