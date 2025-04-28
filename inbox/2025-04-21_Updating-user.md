---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Updating user

```js
// routers/users.js

// ...

router.put('/users/:id', async (req, res) => {
  const { id } = req.params;
  const { bio, username } = req.body

  const user = await UserRepo.update(id, username, bio); // method `update` not yet implemented
  //    ^^^^ for convenience, returns the updated user
  //         if not found, returns undefined

  if (user) {
    res.send(user);
  } else {
    res.sendStatus(404);
  }
});

// ...

```

Then, implement the `update` method in the `UserRepo` class:

```js
// repositories/user-repo.js

// ...

class UserRepo {

  // ...

  static async update(id, username, bio) {
    const { rows } = await pool.query(
      'UPDATE users SET username = $1, bio = $2 WHERE id = $3 RETURNING *;',
      [username, bio, id]
    );

    return toCamelCase(rows)[0]; // if not found, rows will be empty array and [][0] will be undefined
  }

  // ...
}

module.exports = UserRepo;
```
