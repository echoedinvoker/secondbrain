---
date: 2025-01-24
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Adding Some Data

Course provides a .sql file which is a **backup** of the database instead of a **dump**. The difference is that the backup file contains the data and the schema. But we only need to import the data from it.

```bash
sudo -u postgres pg_restore --data-only --disable-triggers -d instagram ig.sql
``` 

The above command is to import only the data from `ig.sql` file to the database `instagram`.

It's easy with PGAdmin. But with the terminal, it's a bit tricky. You may need ask for help from the internet or AI **a lot**.

