---
date: 2025-04-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Dangers Around Data Migrations

In [[2025-04-07_Schema-vs-Data-Migrations|Schema vs Data Migrations]], we said that there are some controversial operations in data migration.

There are several reasons to not run data migrations at the same time as schema migrations - we're going to focus on the most important one.


There are two ways to archieve the requirement in [[2025-04-07_Schema-vs-Data-Migrations|Schema vs Data Migrations]]:

## Everything in single migration

Migration#4
- add column `loc`
- copy lat/lng to `loc`
- drop column `lat` and `lng`


## Split over separate migrations (or not to run them at the same time)

Migration#4 - add column `loc`

??? - copy lat/lng to `loc`

Migration#5 - drop column `lat` and `lng`


## The "everything in single migration" approach will meet the problems

### operations
Add column `loc`
-instantly-> add column complete
Copy from lat/lng to `loc`
------- Copying millions of values! This take long time! ----> copy complete
Drop column `lat` and `lng`
-instantly-> drop column complete

### BIG problem
These operations work in a transaction, because copying takes a long time, there is big chance that server API got multiple requests to create new records in the `posts` table at the same time, but these records won't magically be copied into the transaction, so when the transaction is committed, the new records will got the null value for `loc` column, and the lat/lng columns will be dropped, so the new records have no way to compute the `loc` value after the migration is completed.


