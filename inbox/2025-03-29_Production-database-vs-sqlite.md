---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Production database vs sqlite

Production databases are so called DBMS (Database Management Systems) such as MySQL, PostgreSQL, Oracle, etc.


## SQLite

- SQLite strives to provide local data storage for individual applications and devices.

- SQLite emphasizes economy, efficiency and simplicity. What this means is that SQLite is easy to use and easy to set up.

- SQLite is for most small / medium applications, it works perfectly.

- SQLite focues on different concepts than a DBMS.


## Production databases (DBMS)

- Production databases focus on a big difference compared to SQLite.

- Production databases focus on scalability, concurrency and control. But this only comes into play when dealing with many, many users.

- If you application is going to have 10s of thousands of users, it may be wise to switch to a production database.

- If you application is only you, and a few others, SQLite will work great.

- And if your application becomes popular, you can always switch to a production database later on. If you start with SQLite.


## Key notes

- SQLite runs in-memory or local disk, which allows development of a SQLite data to be easy, as it is part of your application.

- Production databases run on their own server and port. Which means you need to make sure the database is running, and have authentication linking to the DBMS.

- (SQLite) For deployment you can deploy a SQLite database along with the application.

- (Prod DBMS) For deployment you will need to also deploy the database separate from the application.

- It's not saying that you can't deploy a production database with small applications, it's just that SQLite is easier to use and setup for small applications.

