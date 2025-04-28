---
date: 2025-04-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Handling SQL Injection with Prepared Statements

## Options for Handling SQL Injection

For resolving [[2025-04-18_SQL-Injection-Exploits|SQL Injection Exploits]], we have two options:

1. Add code to sanitize user-provided values to our app.

2. Rely on PostgreSQL to sanitize values for us.

Because the first option requires writing complex logic to handle all possible SQL Injection attacks, we usually choose the second option, which is to use prepared statements. (However, there are still some cases where only the first option can be chosen)


## Using Prepared Statements (Option#2)

Assuming we have a SQL query that looks like this:

```sql
SELECT * FROM users WHERE id = 127;
--                             ^^^ this value comes from user input
```

We can split the query into two parts:

**Statement**
```sql
SELECT * FROM users WHERE id = $1;
--                             ^^ means pointer to the first value in the list of values
```

**Values**
```sql
['127']
```

We send the statement and the values to `pg`, and `pg` create a `prepared` statement and send it to PostgreSQL. After that, `pg` will send `EXECUTE` statement with the values to PostgreSQL.

**Create a prepared statement**
```sql
PREPARE asdfuio(string) AS
  SELECT *
  FROM users
  WHERE id = $1;
```

**Execute a prepared statement**
```sql
EXECUTE asdfuio('127');
```

Both of the above statements will be generated and sent to PostgreSQL by `pg`, so PostgreSQL will know that '127' is a value rather than a query. PostgreSQL will generate a final query like this:

```sql
SELECT * FROM users WHERE id = '127';
```
And then execute it.


## Malicious User Input

If a malicious user uses SQL injection as follows:

**Statement**
```sql
SELECT * FROM users WHERE id = $1;
```
**Values**
```sql
['127; DROP TABLE users;']
```
The final query will be:
```sql
SELECT * FROM users WHERE id = '127; DROP TABLE users;';
--                              ^^^^^^^^^^^^^^^^^^^^^^
--                              this will be treated as a value, not query
```
Therefore, `DROP TABLE users;` will not be executed as a query.


## Senario that requires Option#1

However, because Values can only be used to replace literal values in a Statement, if we want the user to input identifiers to replace in the Query, we can only use Option#1 to handle it.

```sql
SELECT * FROM users WHERE id = $1;
--            ^^^^^ if we want to replace this, we have to use Option#1
```
