---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Assertions Around User Count

```sh
 tree src
src
├── app.js
├── pool.js
├── repos
│   ├── user-repo.js
│   └── utils
│       └── to-camel-case.js
├── routes
│   └── users.js
└── test # new directory for tests
    └── routes # corresponding to the routes directory
        └── users.test.js # new file for test codes of users route
```

```js
// src/test/routes/users.test.js
const request = require('supertest'); // supertest is a library to simulate HTTP requests
const buildApp = require('../../app'); // buildApp is a function that creates an Express app
const UserRepo = require('../../repos/user-repo'); 

// create a test for the route to create a user
it('create a user', async () => {

  // we check the count of users before and after the request (there are many approaches to test, this is one of them)
  // we choose it because it is easy to simulate a tests inteference senario
  const startingCount = await UserRepo.count();
  //                                  ^^^^^^^^ not yet implemented
  expect(startingCount).toEqual(0)

  // we create a express app and send a post request to the /users route by supertest for testing purpose
  await request(buildApp())
    .post('/users')
    .send({ username: 'testuser', bio: 'test bio' })
    .expect(200)

  // the count of users should be 1 after the request
  const finishCount = await UserRepo.count();
  expect(finishCount).toEqual(1)
})
```

Because the method `UserRepo.count()` is not yet implemented, let's implement it in the `user-repo.js` file. 

```js
// ...

class UserRepo {

  // ...

  static async count() {
    const { rows } = await pool.query(
      'SELECT COUNT(*) FROM users;'
    ) // rows content should be like: [{ count: 294 }]

    return rows[0].count
  }
}

module.exports = UserRepo;
```

We'll meet a lot of problems when we run the test above, we'll fix them one by one in the future.
