---
date: 2025-01-12
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Thinking About Validation

In general, users will use a web application to fill out a form and submit it to enter data. At this time, the web server will validate this data to ensure its accuracy. This process is called validation.

Validation will prevent invalid data from entering the database and inform the user of any errors that occur.

![web-server-validation.png](../assets/imgs/web-server-validation.png)


But in some early startup companies, there may not be a web application, and users input data directly into the database using any database client, without any validation mechanism.


![client-to-db-insert.png](../assets/imgs/client-to-db-insert.png)

In fact, there are methods to add validation at the database level. We will discuss this issue in this chapter.
