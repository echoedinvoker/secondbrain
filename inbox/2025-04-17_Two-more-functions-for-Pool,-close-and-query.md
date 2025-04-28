---
date: 2025-04-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Two more functions for Pool, close and query

In addition to `connect`, we need to add two more methods, `close` and `query`, to complete the functionality of `Pool`.

```js
// pool.js
const pg = require('pg');

class Pool {
  _pool = null;

  connect(options) {
    this._pool = new pg.Pool(options);
    return this._pool.query('SELECT 1 + 1;')
  }

  // used to close the pool, disconnect from the database entirely
  close() {
    return this._pool.end();
  }

  // used to run a query against the database
  query(sql) {
    return this._pool.query(sql);
  }
}

module.exports = new Pool();
```

The `query` method in the above code actually has significant security issues, which we will discuss in other topics.


