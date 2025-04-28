---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Deleting user

```js
// ...

router.delete('/users/:id', async (req, res) => {
  const { id } = req.params;

  const user = await UserRepo.delete(id); // `delete` method not implemented yet
  //    ^^^^ return deleted user for convenience
  //         if not found, return undefined

  if (user) {
    res.send(user);
  } else {
    res.sendStatus(404);
  }
});

// ...

```

Then, in the `UserRepo` class, implement the `delete` method:

```js
// ...

class UserRepo {

  // ...

  static async delete(id) {
    const { rows } = await pool.query(
      'DELETE FROM users WHERE id = $1 RETURNING *;',
      [id]
    )

    return toCamelCase(rows)[0];
  }
}

module.exports = UserRepo;

```

Test:

```sh
$ curl -X DELETE http://localhost:3005/users/5 | jq

# {
#   "id": 5,
#   "createdAt": "2025-04-18T08:45:44.764Z",
#   "updatedAt": "2025-04-18T08:45:44.764Z",
#   "bio": "This is a test bio",
#   "username": "testuser"
# }

```
