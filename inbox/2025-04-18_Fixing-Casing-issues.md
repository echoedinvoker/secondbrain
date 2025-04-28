---
date: 2025-04-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Fixing Casing issues

We want to fix the [[2025-04-18_Casing-issues|Casing issues]].

```js
// src/repos/user-repo.js

const pool = require('../pool');

class UserRepo {
  static async find() {
    const { rows } = await pool.query('SELECT * FROM users');

    // iterate over the rows and replace the snakecase keys with camelcase keys
    const parsedRows = rows.map((row) => {
      const replaced = {};

      // iterate over the keys of the row and replace the snakecase keys with camelcase keys
      for (const key in row) {
        const camelCaseKey = key.replace(
          /([-_][a-z])/gi, // for example: created_at => match is `_a`
          (match) => match.toUpperCase().replace('_', '') // for example: `_a` => `A`
        ); // for example: `created_at` => `createdAt`

        replaced[camelCaseKey] = row[key]; 
      }
      return replaced;
    })

    return parsedRows;
  }
}

module.exports = UserRepo;
```

Let's test the code.

```sh
$ npm start
```

```sh
$ curl http://localhost:3005/users | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   265  100   265    0     0  24920      0 --:--:-- --:--:-- --:--:-- 26500
[
  {
    "id": 1,
    "createdAt": "2025-04-18T00:59:23.371Z",
#    ^^^^^^^^^
    "updatedAt": "2025-04-18T00:59:23.371Z",
#    ^^^^^^^^^
    "bio": "This is my bio",
    "username": "Alyson14"
  },
  {
    "id": 2,
    "createdAt": "2025-04-18T00:59:23.371Z",
#    ^^^^^^^^^
    "updatedAt": "2025-04-18T00:59:23.371Z",
#    ^^^^^^^^^
    "bio": "This is about me!",
    "username": "Gia67"
  }
]
```

You can see that the `created_at` and `updated_at` keys are replaced with `createdAt` and `updatedAt` respectively.

Next, we can extract the logic of replacing the snakecase keys with camelcase keys into a separate file because we will use this logic in other places as well.

```sh
 tree
.
├── index.js
├── migrations
├── node_modules
├── package.json
├── package-lock.json
└── src
    ├── app.js
    ├── pool.js
    ├── repos
    │   ├── user-repo.js
    │   └── utils # new directory
    │       └── to-camel-case.js # new file for the function of replacing snakecase keys with camelcase keys
    └── routes
        └── users.js # extract the logic from this file
```

```js
// src/repos/utils/to-camel-case.js
module.exports = (rows) => {
  return rows.map((row) => {
    const replaced = {};
    for (const key in row) {
      const camelCaseKey = key.replace(/([-_][a-z])/gi, (match) =>
        match.toUpperCase().replace("_", ""),
      );
      replaced[camelCaseKey] = row[key];
    }
    return replaced;
  });
};
```

```js
// src/repos/user-repo.js
const pool = require('../pool');
const toCamelCase = require('./utils/to-camel-case'); // import the function

class UserRepo {
  static async find() {
    const { rows } = await pool.query('SELECT * FROM users');

    // the logic of replacing the snakecase keys with camelcase keys is extracted to a separate file

    return toCamelCase(rows) // directly use the function to replace the snakecase keys with camelcase keys
  }
}

module.exports = UserRepo;
```

Let's test the code again, it should work as before.

```sh
 curl http://localhost:3005/users | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   265  100   265    0     0  20081      0 --:--:-- --:--:-- --:--:-- 20384
[
  {
    "id": 1,
    "createdAt": "2025-04-18T00:59:23.371Z",
    "updatedAt": "2025-04-18T00:59:23.371Z",
    "bio": "This is my bio",
    "username": "Alyson14"
  },
  {
    "id": 2,
    "createdAt": "2025-04-18T00:59:23.371Z",
    "updatedAt": "2025-04-18T00:59:23.371Z",
    "bio": "This is about me!",
    "username": "Gia67"
  }
]
```
