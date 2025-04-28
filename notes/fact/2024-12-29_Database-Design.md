---
date: 2024-12-29
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Database Design

[wiki cities](https://en.wikipedia.org/wiki/List_of_largest_cities)

In the above page, there is a table about cities. We only need the information of city, country, population (urban area), and area (urban area) for each city, and store it in the database.

To design a database, we will go through the following process:

![process-to-design-db.png](../../assets/imgs/process-to-design-db.png)

For this case, we got the following result:

![result-db-design.png](../../assets/imgs/result-db-design.png)


Then, we can start to create the database step by step as follows:

1. create a database and table

![create-db-table.png](../../assets/imgs/create-db-table.png)


2. set fields of the table (columns)

![fields-of-table.png](../../assets/imgs/fields-of-table.png)


3. insert data into the table (rows, records)

![insert-record-to-table.png](../../assets/imgs/insert-record-to-table.png)


