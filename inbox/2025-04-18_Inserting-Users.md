---
date: 2025-04-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Inserting Users

We want to complete the endpoint for inserting users.

```javascript
// src/routes/users.js

// ...

router.post('/users', async (req, res) => {
  console.log(req.body);
  //          ^^^^^^^^ it'll get the parsed body by the body-parser middleware

  res.send('');
});

// ...

```

```sh
curl -X POST \
  http://localhost:3005/users \
  -H "Content-Type: application/json" \
  -d '{
    "bio": "This is a test bio",
    "username": "testuser"
  }'
```
Backend logs:
```js
{ bio: 'This is a test bio', username: 'testuser' }
```

We can the request body already parsed to an object by the body-parser middleware.

Then, we can complete the endpoint by inserting the user into the database.

```js
// src/routes/users.js

// ...

router.post('/users', async (req, res) => {
  const { bio, username } = req.body;

  const user = await UserRepo.insert(username, bio); // use the users repository to insert the user (.insert not yet implemented)
//      ^^^^ usually, we hope to get the user back from the database

  res.send(user);
  //       ^^^^ and then send it back to the client
});

// ...

```
Implement the insert method in the users repository.

```js
// src/repos/user-repo.js

// ...

class UserRepo {
  static async find() { ... }

  static async findById(id) { ... }

  // create the insert method
  static async insert(username, bio) {
    const { rows } = await pool.query(
      'INSERT INTO users (username, bio) VALUES ($1, $2) RETURNING *',
      //                                         ^^^^^^^ ^^^^^^^^^^^ returning the inserted user
      //                                         ^^^^^^^ username and bio come from the user input,
      [username, bio]  //                                so we should always use prepared statements to avoid SQL injection
    )

    return toCamelCase(rows)[0]; // remember parse the result rows to camel case keys
    //                      ^^^ because only one user is inserted and returned
  }
}

module.exports = UserRepo;
```

Let's test it again.

```sh
$ curl -X POST \
        http://localhost:3005/users \
        -H "Content-Type: application/json" \
        -d '{
      "bio": "This is a test bio",
      "username": "testuser"
    }' | jq

# {
#   "id": 5,
#   "createdAt": "2025-04-18T08:45:44.764Z",
#   "updatedAt": "2025-04-18T08:45:44.764Z",
#   "bio": "This is a test bio",
#   "username": "testuser"
# }

```

You can see that the user is inserted into the database and returned in the response.
