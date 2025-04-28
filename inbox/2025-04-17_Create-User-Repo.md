---
date: 2025-04-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Create User Repo

In [[2025-04-17_Repository-pattern|Repository pattern]], we've already learned about the concept of repository pattern. Now, we want to create a user repository.

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
    ├── repos # new folder for repositories
    │   └── user-repo.js # new file for user repository
    └── routes
```

We have multiple ways to create a user repository.

1. plain object

```js
module.exports = {
  find() {},
  findById(id) {},
  ...
}
```

2. instance methods of a class

```js
class UserRepo {
  find() {}
  findById(id) {}
  ...
}

module.exports = new UserRepo();
```

3. static methods of a class

```js
class UserRepo {
  static find() {}
  static findById(id) {}
  ...
}

module.exports = UserRepo;
//              ^ be careful, there is no `new` here because we don't want to create an instance of the class
```

There's not really a great reason to use one way over the other. It really comes down to kind of your personal preference. I prefer the static methods of a class because it makes it clear.


## User Repository by Static Methods

```js
const pool = require('../pool');

class UserRepo {
  static async find() {
    const { rows } = await pool.query('SELECT * FROM users');
    //    ^^^^^^^^ it'll resolve a lots of informations about the query, but we only care about the rows

    // maybe do some parsing or validation here (?)

    return rows;
  }
}

module.exports = UserRepo;
```

Then, we can use it in our endpoint.

```js
// src/routes/user.js

// ...

router.get('/users', async (req, res) => {
  const users = await UserRepo.find(); // use user repository to find all users

  res.send(users);
});

// ...

```

Finally, we can launch the server and test it.

```sh
$ npm start
```

```sh
$ curl http://localhost:3005/users
[] # no users yet, but the query works

```
