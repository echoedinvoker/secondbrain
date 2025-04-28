---
date: 2025-04-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Finding Particular Users (Bad practice)

We want to implement a endpoint to find a particular user by their id.

```js
// src/routes/users.js

// ...

router.get('/users/:id', async (req, res) => {
  const { id } = req.params;

  const user = await UserRepo.findById(id); // use user repository method to find user by id (not yet implemented)

  // because the result of the findById method might be undefined (user not found), we need to check if the user exists
  // and return a 404 status code if not
  if (user) {
    res.send(user);
  } else {
    res.sendStatus(404);
  }
});

// ...

```

Let's implement the `findById` method in the user repository.

```js
const pool = require('../pool');
const toCamelCase = require('./utils/to-camel-case');

class UserRepo {
  static async find() { ... }

  // create new method to find user by id
  static async findById(id) {

    // this is a bad practice, we'll explain why in the next topic
    const { rows } = await pool.query(`SELECT * FROM users WHERE id = ${id}`);
    //      ^^^^ we'll got a array with single object of user,
    //           or an empty array if user not found

    return toCamelCase(rows)[0];
    //     ^^^^^^^^^^^^^^^^^^^^ no matter if the user is found or not, we parse the result to camel case key
    //                          if not found, [0] will be undefined
  }
}

module.exports = UserRepo;
```

Let's test the endpoint.

```bash
$ curl http://localhost:3005/users/1 | jq
{
  "id": 1,
  "createdAt": "2025-04-18T00:59:23.371Z",
  "updatedAt": "2025-04-18T00:59:23.371Z",
  "bio": "This is my bio",
  "username": "Alyson14"
}

$ curl http://localhost:3005/users/1234
Not Found

```

Even the test results are correct, this is a bad practice. There is a big security issue with this code.


