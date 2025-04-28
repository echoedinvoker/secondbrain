---
date: 2025-04-23
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Assumptions Around Content

In [[2025-04-23_Multi-DB-Setup|Multi DB Setup]], we create a seperate database for testing, but there is no any structure inside it. So, we need to run all the migrations to create the structure.

```sh
$ DATABASE_URL=postgres://postgres@localhost:5432/socialnetwork_test npm run migrate up
#                                                 ^^^^^^^^^^^^^^^^^^ run migrate on the test db
```

Now, test database has the same structure as the main database. We can run the tests now.

```sh
$ npm run test

 FAIL  src/test/routes/users.test.js
  ✕ create a user (2 ms)

  ● create a user

    expect(received).toEqual(expected) // deep equality

    Expected: 0
    Received: "0"

      22 | it('create a user', async () => {
      23 |   const startingCount = await UserRepo.count();
    > 24 |   expect(startingCount).toEqual(0)
         |                         ^
      25 |
      26 |   await request(buildApp())
      27 |     .post('/users')

      at Object.toEqual (src/test/routes/users.test.js:24:25)
```

That's because the returned value from `UserRepo.count()` is a string, not a number. We need to convert it to a number before comparing it.

Convertion should be done in the `UserRepo` class, not in the test.

```javascript
// src/repos/user-repo.js

// ...

class UserRepo {

  // ...

  static async count() {
    const { rows } = await pool.query(
      'SELECT COUNT(*) FROM users;'
    )

    return parseInt(rows[0].count)
    //     ^^^^^^^^ convert the string to a number
  }
}

module.exports = UserRepo;
```

Now, we can run the tests again.

```sh
$ npm run test

 PASS  src/test/routes/users.test.js
  ✓ create a user (29 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.255 s, estimated 1 s
Ran all test suites.

$ npm run test

 FAIL  src/test/routes/users.test.js
  ✕ create a user (3 ms)

  ● create a user

    expect(received).toEqual(expected) // deep equality

    Expected: 0
    Received: 1

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
Time:        0.189 s, estimated 1 s
Ran all test suites.
```

You can see that the first time the test was run, it passed, but the second time the test was run, it failed because we created a user in the database during the first test run, so the startingCount was not 0 the second time.

Perhaps we can assume in the test that the initial state of the database is not empty to solve the above issue.


```javascript
// src/test/routes/users.test.js

// ...

it('create a user', async () => {
  const startingCount = await UserRepo.count();
  //expect(startingCount).toEqual(0) <----------- this line assume that the database is empty, we remove it

  await request(buildApp())
    .post('/users')
    .send({ username: 'testuser', bio: 'test bio' })
    .expect(200)

  const finishCount = await UserRepo.count();
  expect(finishCount - startingCount).toEqual(1)
  //                 ^^^^^^^^^^^^^^^ instead of expecting the count of finish, we expect the difference between the finish and starting count
  //                                 So we don't need to worry about the database being empty or not at the beginning
})
```
Now, this test will pass even if the database is not empty at the beginning.

> So, we got a conclusion that assuming the initial state of the database is not empty is a good practice.

But maybe we will meet other tests that need to be run in a clean database. We will create more tests in the following topics.

