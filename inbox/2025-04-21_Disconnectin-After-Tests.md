---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Disconnectin After Tests

In [[2025-04-21_Connecting-to-a-DB-For-Tests|Connecting to a DB For Tests]], the reason that the test program does not exit after all tests are done is because the connection to the database is not closed. We need to close the connection after all tests are done with the `afterAll` method of Jest.

```js
// src/test/routes/users.test.js

// ...

beforeAll(() => { ... });

// `afterAll` is a Jest function that runs after all tests are done
afterAll(() => {
  return pool.close(); // `close` is a method we created in the pool module
                       // we can use it to close the connection pool
});

// ...tests

```

Because we didn't let express app to listen to any port, so if the connection pool is closed and no any tasks are in queue, the node program will exit automatically.

```sh
$ npm run test

 FAIL  src/test/routes/users.test.js
  ✕ create a user (3 ms)

  ● create a user

    expect(received).toEqual(expected) // deep equality

    Expected: 0
    Received: "4"

      22 | it('create a user', async () => {
      23 |   const startingCount = await UserRepo.count();
    > 24 |   expect(startingCount).toEqual(0)
         |                         ^
      25 |
      26 |   await request(buildApp())
      27 |     .post('/users')

      at Object.toEqual (src/test/routes/users.test.js:24:25)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.238 s, estimated 1 s
Ran all test suites.

$        

```

Although test is still failing, but at least the program exits after all tests are done now :D



