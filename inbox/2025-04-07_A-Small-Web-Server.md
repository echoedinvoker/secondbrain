---
date: 2025-04-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Small Web Server

We want to create a very small web application to represent the posts from the database and submit new posts into the database. So we can do the `code update` steps in [[2025-04-07_Properly-Running-Data-and-Schema-Migrations|Properly Running Data and Schema Migrations]].

In order to connect to Postgres and create endpoints to accept the request from the browser, we need to install the following packages:

```bash
 npm install express pg
```

Then, we create a file called `index.js` to connect to Postgres.

```js
// import the library `pg`, which is a Postgres client for Node.js
const pg = require('pg');

// create a new pool, which is a collection of connections to the database
// we can use the pool to execute queries
const pool = new pg.Pool({
  host: 'localhost',
  port: 5432,
  database: 'socialnetwork',
  user: 'postgres',
  password: ''
});

// test the connection
pool.query('SELECT 1 + 1;').then(res => console.log(res));
```

Then we can run the file to test the connection:

```bash
 node index.js
Result {
  command: 'SELECT',
  rowCount: 1,
  oid: null,
  rows: [ { '?column?': 2 } ],
  fields: [
    Field {
      name: '?column?',
      tableID: 0,
      columnID: 0,
      dataTypeID: 23,
      dataTypeSize: 4,
      dataTypeModifier: -1,
      format: 'text'
    }
  ],
  _parsers: [ [Function: parseInteger] ],
  _types: TypeOverrides {
    _types: {
      getTypeParser: [Function: getTypeParser],
      setTypeParser: [Function: setTypeParser],
      arrayParser: [Object],
      builtins: [Object]
    },
    text: {},
    binary: {}
  },
  RowCtor: null,
  rowAsArray: false,
  _prebuiltEmptyResultObject: { '?column?': null }
}
```

Connecting to the database is successful. Now we can create two endpoints by using the `express` library.

```js
const express = require('express'); // import the library `express`, which is a web framework for Node.js
const pg = require('pg');

const pool = new pg.Pool({
  host: 'localhost',
  port: 5432,
  database: 'socialnetwork',
  user: 'postgres',
  password: ''
});

const app = express(); // create a new express application
app.use(express.urlencoded({ extended: true })); // add middleware to parse the request body, so we can access the data in the request body

// create a endpoint to get the posts from the database and display them in a table
app.get('/posts', async (req, res) => {
  // query the database to get the posts
  const { rows } = await pool.query(`
    SELECT * FROM posts;
  `);

  // render the posts in a table
  res.send(`
    <table>
      <thead>
        <tr>
          <th>id</th>
          <th>lng</th>
          <th>lat</th>
        </tr>
      </thead>
      <tbody>
        ${rows.map(row => {
          return `
            <tr>
              <td>${row.id}</td>
              <td>${row.lng}</td>
              <td>${row.lat}</td>
            </tr>
          `;
        }).join('')}
      </tbody>
    </table>
    <form method="POST">
      <h3>Create Post</h3>
      <div>
        <label>Lng</label>
        <input name="lng" />
      </div>
      <div>
        <label>Lat</label>
        <input name="lat" />
      </div>
      <button type="submit">Create</button>
    </form>
  `)
})

// create an endpoint to get lng, lat data from the request body and insert it into the database
// this endpoint will be called when the form is submitted, it's default behavior of the form
app.post('/posts', async (req, res) => {
  const { lng, lat } = req.body;
  await pool.query('INSERT INTO posts (lng, lat) VALUES ($1, $2)', [lng, lat]);
  res.redirect('/posts'); // this line will call the first endpoint to get the table with new data from the database
})

// let the app listen on port 3005
app.listen(3005, () => {
  console.log('Server is running on http://localhost:3005');
});
```

Now, we can open the browser and go to `http://localhost:3005/posts`, you'll see a table with the posts from the database and a form to create new posts.


