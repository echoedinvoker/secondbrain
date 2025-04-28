---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Connecting to a DB For Tests

Let's create a script of `package.json` to run the test we wrote in [[2025-04-21_Assertions-Around-User-Count|Assertions Around User Count]].

```json
{
  ...
  "scripts": {
    "migrate": "node-pg-migrate",
    "start": "nodemon index.js",
    "test": "jest" <----------------- this is the test script
  },
  ...
}
```

And run the test with it:
```sh
$ npm run test

 FAIL  src/test/routes/users.test.js
  ✕ create a user (1 ms)

  ● create a user

    TypeError: Cannot read properties of null (reading 'query')

      14 |
      15 |   query(sql, params) {
    > 16 |     return this._pool.query(sql, params);
         |                       ^
      17 |   }
      18 | }
      19 |

      at Pool.query (src/pool.js:16:23)
      at Function.query (src/repos/user-repo.js:48:33)
      at Object.count (src/test/routes/users.test.js:6:40)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.179 s, estimated 1 s
Ran all test suites.
```

The error is because we are trying to use the `query` method of the `pool` object, but it is not initialized yet by calling `connect()`. We need to do that before running the test. We can do that in the `beforeAll` method of Jest.

```js
// src/test/routes/users.test.js
const request = require('supertest');
const buildApp = require('../../app');
const UserRepo = require('../../repos/user-repo');
const pool = require('../../pool'); // import

// beforeAll take a function as an argument, which will be called before all tests
beforeAll(() => {
  return pool.connect({
//^^^^^^^^^^^^^^^^^^^ it'll return a promise, only after this promise is resolved, the test will run

    // now, we just copy the connection parameters from the index.js file (this is not a good practice, we'll see how to fix it in the future)
    host: 'localhost',
    port: 5432,
    database: 'socialnetwork',
    user: 'postgres',
    password: ''
  });
});

// ...tests

```

Now, we can run the test again:
```sh
$ npm run test

 FAIL  src/test/routes/users.test.js
  ✕ create a user (10 ms)

  ● create a user

    expect(received).toEqual(expected) // deep equality

    Expected: 0
    Received: "4"  # because we connected to the DB and it already has 4 users, so the count is 4 and leads to a failure test

      16 | it('create a user', async () => {
      17 |   const startingCount = await UserRepo.count();
    > 18 |   expect(startingCount).toEqual(0)
         |                         ^
      19 |
      20 |   await request(buildApp())
      21 |     .post('/users')

      at Object.toEqual (src/test/routes/users.test.js:18:25)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.247 s, estimated 1 s
Ran all test suites.
Jest did not exit one second after the test run has completed.

# below sentence is a warning about the test not exiting after all tests are done
This usually means that there are asynchronous operations that weren't stopped in your tests. Consider running Jest with `--detectOpenHandles` to troubleshoot this issue.
```

From the above results, it can be seen that although the issue of null pool has been resolved, two new problems have arisen:
1. The connected database isn't empty at the beginning, so the test fails
2. The test is not exiting after all tests are done

We'll fix them one by one in the future.


