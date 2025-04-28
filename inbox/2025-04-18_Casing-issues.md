---
date: 2025-04-18
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Casing issues

We insert two pieces of data into the users table as follows

```sh
socialnetwork=#
INSERT INTO users (bio, username)
VALUES
    ('This is my bio', 'Alyson14'),
    ('This is about me!', 'Gia67');
# INSERT 0 2

socialnetwork=# SELECT * FROM users;

#  id |          created_at           |          updated_at           |        bio        | username
# ----+-------------------------------+-------------------------------+-------------------+----------
#   1 | 2025-04-18 00:59:23.371129+00 | 2025-04-18 00:59:23.371129+00 | This is my bio    | Alyson14
#   2 | 2025-04-18 00:59:23.371129+00 | 2025-04-18 00:59:23.371129+00 | This is about me! | Gia67
# (2 rows)
```

You can see that there are two column names `created_at` and `updated_at` that are using snakecase naming convention, which is the convention used in databases. However, in JavaScript, camelcase naming convention is used.

```js
const pool = require('../pool');

class UserRepo {
  static async find() {
    const { rows } = await pool.query('SELECT * FROM users');

    rows[0].create_at;  // this will break the consistency of naming convention in JS

    return rows;
  }
}

module.exports = UserRepo;
```

In order to fix this, we have three options:

1. Allow the use of snakecase naming convention in JavaScript, but this will lead to inconsistency.

2. Use camelcase naming convention in the database, but this will break the convention used in databases.

3. Whenever we query the database and get the data, we can do some processing or parsing to convert the snakecase naming convention to camelcase naming convention. This is the best option as it will keep the consistency of naming convention in both JavaScript and database.


