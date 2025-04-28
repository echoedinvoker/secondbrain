---
date: 2025-04-14
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Updating Values

We choose Option #2 in the [[2025-04-14_Concept-of-step-3:-Transaction-Locks|Concept of step 3: Transaction Locks]] to generate the value of the loc field.

We can directly use tools like pgAdmin to update the values in the database or use JS. Generally, the latter is better because JS codes can be version controlled, so other engineers can also know that they need to use this JS code to update the values in the database. Additionally, using JS codes on a production instance will be more convenient.

```sh
 tree
.
├── index.js
├── migrations
│   ├── 1741411109659_table-comments.js
│   ├── 1743996744179_rename-contents-to-body.js
│   ├── 1744010673416_add-new-table-posts.js
│   ├── 1744599156797_add-loc-to-posts.js
│   └── data # new directory
│       └── 01-lat-lng-to-loc.js # new file to update values
├── node_modules
├── package.json
└── package-lock.json
```

Above, we add a `data` directory in the `migrations` directory specifically for storing JS code to update database values. This code is more related to migrations than the main app.

```js
const pg = require('pg');

const pool = new pg.Pool({
  host: 'localhost',
  port: 5432,
  database: 'socialnetwork',
  user: 'postgres',
  password: ''
});

// Update the loc field with lat and lng
pool.query(`
  UPDATE posts
  SET loc = POINT(lng, lat)
  WHERE loc IS NULL;
`)
  .then(() => {
    console.log('Updated loc completely');
    pool.end(); // disconnect from the database
  })
  .catch(err => console.error(err.message));
  
```

Then, we can execute that file using the command below:

```sh
$ cd migrations/data
$ node 01-lat-lng-to-loc.js
```

Let's check the database to see if the values are updated correctly.
```sh
 psql -U postgres -d socialnetwork

socialnetwork=# SELECT * FROM posts;
 id | url | lat | lng |   loc   
----+-----+-----+-----+---------
  4 |     |  34 |  12 | (12,34)
  1 |     |  22 |  11 | (11,22)
  2 |     |  44 |  33 | (33,44)
  3 |     |  66 |  55 | (55,66)
#                       ^^^^^^^ all records have loc values now

