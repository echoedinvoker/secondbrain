---
date: 2025-04-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Validating Connection Credentials

We need to wait for the pool to make sure it has contacted the database before letting the app listen on port 3005. We will create a new file index.js to implement this.

```sh
 tree
.
├── migrations
├── node_modules
├── package.json
├── package-lock.json
├── index.js # new file
└── src
    ├── app.js
    ├── pool.js
    └── routes
```

```js
const app = require('./src/app');
const pool = require('./src/pool');

pool.connect({
  host: 'localhost',
  port: 5432,
  database: 'socialnetwork',
  user: 'postgres',
  password: ''
})
```

In fact, the codes above do not connect the pool to the database because without any queries, the pool will not generate a client. Therefore, we need to write a simple query to generate a client in the pool and connect to the database to ensure the connection is normal.

```js
// pool.js
const pg = require('pg');

class Pool {
  _pool = null;

  connect(options) {
    this._pool = new pg.Pool(options);
    return this._pool.query('SELECT 1 + 1;') // add a simple query when connecting, this will return a Promise
  }
}

module.exports = new Pool();
```

Because the connect method returns a Promise, we can use .then/.catch to handle the result of the connection. If the connection is successful, we can start the server. If the connection fails, we can log the error and exit the process.

```js
// index.js
const app = require('./src/app');
const pool = require('./src/pool');

pool.connect({
  host: 'localhost',
  port: 5432,
  database: 'socialnetwork',
  user: 'postgres',
  password: ''
})
  // handle the result of the connection
  .then(() => {
    app().listen(3005, () => {
      console.log('Server is running on port 3005');
    })
  })
  .catch((err) => {
    console.error('Error connecting to the database', err);
  });
```

