---
date: 2025-03-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Migration Files

In [[2025-02-28_A-Story-on-Migrations|the previous topic]], we learned two lessons about changing the database schema, and we can use a way to resolve these issues - migration files.

Before we directly use SQL script to change the database schema, and it occurs some issues.

![update-schema-with-sql.png](../assets/imgs/update-schema-with-sql.png)
So, instead of using SQL scripts, we can use migration files to change the database schema.

![change-schema-with-migration-files.png](../assets/imgs/change-schema-with-migration-files.png)
## What is a Migration File?

**Migration files include two parts: Up and Down.**

![two-parts-of-migration-files.png](../assets/imgs/two-parts-of-migration-files.png)
So, with them, we can do two operations:

![apply-and-revert-schema.png](../assets/imgs/apply-and-revert-schema.png)
As above, we can even build some tables from a empty database with migration files. And use the Down part to revert the database to the initial state (in this case, an empty database).


**Migration files don't tie to a specific language or framework.**


## Instead of giving a database to other engineers, we can give them migration files. 

Therefore, instead of giving other engineers a copy of the database, it is better to give them migration files so that they can build it up from a blank database themselves.

![start-from-empty-by-migration-files.png](../assets/imgs/start-from-empty-by-migration-files.png)
## Fix the problem migration

If any issues are found during the migration process, you can revert to the migration file, modify the problematic migration file, and then reapply it.



