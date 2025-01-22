---
date: 2025-01-15
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Using a SQL Design Tool

Next, we will establish a database structure for the real application, which means we need to create many tables and there will be many complex relationships between them. At this point, we usually use an SQL Schema Design Tool to help us create the database schema.

There are two main types of SQL Schema Design Tools. The first type involves writing configuration files and automatically generating diagrams. The second type provides various drawing tools for users to manually create schemas.

[Schema Deisgn Tool](https://sql.toad.cz/)  is a web-based SQL Schema Design Tool that provides a graphical interface for users to create database schemas. 

Let's create a database schema for the real application using this tool.


![insert-tables.png](../assets/imgs/insert-tables.png)


![insert-field.png](../assets/imgs/insert-field.png)

![insert-foreign-keys.png](../assets/imgs/insert-foreign-keys.png)

![adjust-fk-names.png](../assets/imgs/adjust-fk-names.png)


Then we can export the graphical schema as a PDF or PNG file for others to reference. It is important to note that it does not actually create a database for us, it simply helps us draw the schema diagram.
