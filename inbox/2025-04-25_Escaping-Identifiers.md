---
date: 2025-04-25
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Escaping Identifiers

```js
// ...

beforeAll(async () => {

  // ...

  await pool.query(`CREATE ROLE ${roleName} LOGIN PASSWORD '${roleName}'`);
  
  await pool.query(`CREATE SCHEMA ${roleName} AUTHORIZATION ${roleName}`);
  
  // ...

});

// ...

```

In the test file above, we use template strings in the SQL query. Although the test file will not be used in development or production, so the inserted value is definitely not user-provided. However, if you still want to avoid this kind of code smell, we can rewrite it as follows:

```js
// ...

beforeAll(async () => {

  // ...

  await pool.query('CREATE ROLE $1 LOGIN PASSWORD $2', [roleName, roleName]);
  
  await pool.query('CREATE SCHEMA $1 AUTHORIZATION $1', [roleName]);
  
  // ...

});

// ...

```

However, the rewritten version will instead result in errors, because parameterized query can only be used in SQL literal values, and in SQL, role is an identifier and not a literal value, so this writing will cause an error.

We can use the `pg-format` module to solve this problem. This module can help us format SQL statements and safely handle identifiers and literals in SQL.

```js
// ...

const format = require('pg-format'); // import pg-format module

beforeAll(async () => {
  
  // ...

  await pool.query(format('CREATE ROLE %I LOGIN PASSWORD %L;', roleName, roleName));
  //               ^^^^^^              ^^ using %        ^^    ^^^^^^^^^^^^^^^^^^ not array
  //                                   ^^                ^^ literal value
  //                                   ^^ identifier

  await pool.query(format('CREATE SCHEMA %I AUTHORIZATION %I;', roleName, roleName));
  //                                     ^^               ^^    ^^^^^^^^^^^^^^^^^^ although two placeholders are the same $I
  //                                                                               we still need to pass them separately instead one
  
  // ...

});

// ...

```
