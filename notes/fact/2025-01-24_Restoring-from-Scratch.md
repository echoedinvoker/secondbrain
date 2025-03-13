---
date: 2025-01-24
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Restoring from Scratch

In [[2025-01-24_Adding-Some-Data|this topic]], we discussed how to extract only data from a PostgreSQL backup file to existing database. In this topic, we will discuss how to restore the entire database from a backup file.

> **Note:** Even all files have extension `.sql`, they are not the same. Some files are regular SQL files, and some are PostgreSQL backup files. The regular SQL files are just a series of SQL commands that can be executed to create tables, insert data, etc. The PostgreSQL backup files are created by `pg_dump` command and contain the entire database schema and data. Just be careful when you are dealing with these files.


## Restoring from a Backup File
```bash
pg_restore -U postgres -d ig --clean --if-exists --no-owner --no-privileges ig.sql
```
This command will:

1. Delete existing database objects before restoring (--clean)
2. Ignore errors if the object to be deleted does not exist (--if-exists)
3. Ignore ownership settings (--no-owner)
4. Ignore permission settings (--no-privileges)

In the future, we will change the database structure a lot, so sometimes if we mess up the database, we will need to use the above command to restore the database from scratch.
