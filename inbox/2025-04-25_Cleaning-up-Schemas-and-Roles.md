---
date: 2025-04-25
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Cleaning up Schemas and Roles

We want to create a new method to clean up schemas and roles in the database after tests. Let's create the method and write down the steps at first.


```javascript
// src/test/context.js
const pool = require('../../pool');

const { randomBytes } = require('crypto');
const { default: migrate } = require('node-pg-migrate');
const format = require('pg-format');

class Context {
  static async bulid() { ... }

  constructor(roleName) {
    this.roleName = roleName;
  }

  // create an instance method to clean up the schema and role created in the .build()
  async close() {
    // Disconnect from PG (because we cannot drop the schema while connected to it)
    //                    (so we need to disconnect from the schema and reconnect as the root user)
    
    // Reconnect as our root user

    // Delete the role and schema we created

    // Disconnect from PG
    
  }
}

module.exports = Context;
```

Now let's implement the `close` method.

```js

// ... 

class Context {
  static async bulid() { ... }

  constructor(roleName) {
    this.roleName = roleName;
  }

  async close() {
    // Disconnect from PG
    await pool.close();
    
    // Reconnect as our root user
    await pool.connect({
      host: "localhost",
      port: 5432,
      database: "socialnetwork_test",
      user: "postgres",
      password: "",
    });

    // Delete the role and schema we created
    await pool.query(format(
      'DROP SCHEMA %I CASCADE; DROP ROLE %I;', // note that drop schema must be before drop role
                                               // and it need CASCADE to drop all objects in the schema
      this.roleName,
      this.roleName,
    ))

    // Disconnect from PG
    await pool.close();
  }
}

module.exports = Context;
```

We already do the same connection in the `build` method, so we can extract the option to a variable and reuse it in both methods.

```javascript
```js

// ...

// extract the reused option to a variable
const DEFAULT_OPT = {
  host: "localhost",
  port: 5432,
  database: "socialnetwork_test",
  user: "postgres",
  password: "",
}

class Context {
  static async bulid() {

    // ...

    await pool.connect(DEFAULT_OPT);
    //                 ^^^^^^^^^^^

    // ...
  }

  constructor(roleName) {
    this.roleName = roleName;
  }

  async close() {
   
    // ...
    
    await pool.connect(DEFAULT_OPT);
    //                 ^^^^^^^^^^^

    // ...
  }
}

module.exports = Context;

```

Now we can use the `close` method in our test file, we should use it in the `afterAll` method to clean up the database after all tests are done.

```javascript
// src/test/routes/users.test.js

// ...

let context; // because we want to create the context in the beforeAll and use it in the afterAll
             // so need a global variable to access it in both methods

beforeAll(async () => {
  context = await Context.bulid();
//^^^^^^^

});

afterAll(() => {
  return context.close();
//^^^^^^^^^^^^^^^^^^^^^^^ delete the schema and role and disconnect from PG
//                        here we don't use async/await, but need to return the promise
});

// ...

```
