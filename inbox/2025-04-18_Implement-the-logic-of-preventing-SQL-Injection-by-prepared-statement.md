---
date: 2025-04-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Implement the logic of preventing SQL Injection by prepared statement

In [[2025-04-18_Handling-SQL-Injection-with-Prepared-Statements|Handling SQL Injection with Prepared Statements]], we've learned about the concept of SQL Injection and how prepared statements can help prevent it. Now, let's implement the logic of preventing SQL Injection by using prepared statements in our code.

```js
// src/repos/user-repo.js

// ...

class UserRepo {
  static async find() { ... }

  static async findById(id) {
    const { rows } = await pool.query(
      'SELECT * FROM users WHERE id = $1',
//    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ instead of template string, we use a regular string with a placeholder $1
      [id]
//    ^^^^ this is the value that will be used to execute the prepared statement
    );

    return toCamelCase(rows)[0];
  }
}

module.exports = UserRepo;
```

And becuase `pool` is created by ourself, we need to modify its method `query` to support prepared statements also.

```js
// src/pool.js

const pg = require('pg');

class Pool {
  _pool = null;

  connect(options) { ... }

  close() { ... }

  query(sql, params) {
  //         ^^^^^^
    return this._pool.query(sql, params);
    //                           ^^^^^^
  }
}

module.exports = new Pool();

```

Whenever our queries involve user input, we should use above method to prevent SQL Injection. Even the input comes from our own code, we should still use prepared statements to prevent SQL Injection in any case!


