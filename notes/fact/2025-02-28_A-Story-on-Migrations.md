---
date: 2025-02-28
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Story on Migrations

[[2025-02-28_Window-between-DB-and-Clients|Window between DB and Clients]]
[[2025-02-28_Different-DB-between-different-engineers|Different DB between different engineers]]

Above two stories give us two Big Lessons:

## Big Lesson #1
> Changes to the DB structure and changes to clients need to be made at precisely the same time.

## Big Lesson #2
> Whenever you are working with other engineers, it is extremely important that you have a very easy way to tie the structure of your database or changes to the structure of your database, to your code as well

We will dicuss more about these two lessons in the following topics.
