---
date: 2025-04-25
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Programmatic Schema Creation

We annotate the steps in [[Strategy for Isolation]] in the test file's beforeAll, so we can follow these steps to complete the task.


```javascript
// src/test/routes/users.test.js

// ...

beforeAll(async () => {
  // Randomly generating a role name to connect to PG as 

  // Connect to PG as usual
  await pool.connect({
    host: 'localhost',
    port: 5432,
    database: 'socialnetwork_test',
    user: 'postgres',
    password: ''
  });

  // Create a new role
  
  // Create a schema with the same name
  
  // Disconnect entirely from PG

  // Run our migrations in the new schema

  // Connect to PG as the newly created role
  
});

// ...

```

Then, follow the steps above to write the code:

```js
// ...

const { randomBytes } = require('crypto'); // Importing the randomBytes function from the crypto module
const { default: migrate } = require('node-pg-migrate'); // We want to run our migrations programmatically

beforeAll(async () => {
  // Randomly generating a role name to connect to PG as 
  const roleName = 'a' + randomBytes(4).toString('hex');
  //               ^^^ because the result of `randomBytes(4).toString('hex')` is a string of 8 characters mixed with numbers and letters
  //               ^^^ we need to make sure the role name starts with a letter, so we add 'a' in front of it

  // Connect to PG as usual
  await pool.connect({
    host: 'localhost',
    port: 5432,
    database: 'socialnetwork_test',
    user: 'postgres',
    password: ''
  });

  // Create a new role
  await pool.query(`CREATE ROLE ${roleName} LOGIN PASSWORD '${roleName}'`);
  //                                                       ^           ^ be careful with the single quotes for the password
  
  // Create a schema with the same name
  await pool.query(`CREATE SCHEMA ${roleName} AUTHORIZATION ${roleName}`);

  // Disconnect entirely from PG
  await pool.close();

  // Run our migrations in the new schema
  await migrate({
    schema: roleName,
    direction: 'up',
    log: () => {}, // by default, node-pg-migrate will log the SQL queries to the console, but we don't want that
                   // so we pass an empty function to the log option to prevent it from logging

    noLock: true, // by default, node-pg-migrate will try to acquire a lock on the database to prevent other connections from running migrations at the same time
                  // but there are multiple test files running at the same time, so we need to disable the lock

    dir: 'migrations', // the directory where our migration files are located

    // because we want to migrate the specific schema, so we need to connect with the specific role
    databaseUrl: {  // value of this option is a string or an object, we use an object here
      host: 'localhost',
      port: 5432,
      database: 'socialnetwork_test',
      user: roleName,
      //    ^^^^^^^^
      password: roleName
      //        ^^^^^^^^
    }
  });

  // Connect to PG as the newly created role
  await pool.connect({
    host: 'localhost',
    port: 5432,
    database: 'socialnetwork_test',

    // connect with the newly created role, and by default, it will be redirected to the schema with the same name
    user: roleName,
    password: roleName
  });
});

// then, our tests in this file should work on the new schema we just created

// ...
