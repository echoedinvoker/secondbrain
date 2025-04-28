---
date: 2025-04-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# First migration

We need to create a migration for this section to generate the table `users`.

```sh
 mkdir -p app/social-repo
 cd app/social-repo/
 npm init -y
 npm install dedent express jest node-pg-migrate nodemon pg pg-format supertest
```

Then, we need to create scripts in package.json for using `node-pg-migrate` and `nodemon`.

```json
{
  "name": "social-repo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "migrate": "node-pg-migrate",  <-- This is the command to run migrations
    "start": "nodemon index.js" <-- This is the command to start the server
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "type": "commonjs",
  "dependencies": {
    "dedent": "^1.5.3",
    "express": "^5.1.0",
    "jest": "^29.7.0",
    "node-pg-migrate": "^7.9.1",
    "nodemon": "^3.1.9",
    "pg": "^8.14.1",
    "pg-format": "^1.0.4",
    "supertest": "^7.1.0"
  }
}
```

Now, we can create the migration file.

```sh
 npm migrate create add users table
```

This will create a file in the `migrations` folder, we can edit it to add the `users` table and drop it.

```js
//...
exports.up = (pgm) => {
  pgm.sql(`
    CREATE TABLE users (
      id SERIAL PRIMARY KEY,
      created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
      updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
      bio VARCHAR(400),
      username VARCHAR(30) NOT NULL
    );
  `)
};

exports.down = (pgm) => {
  pgm.sql(`
    DROP TABLE users;
  `)
};
```
Now, we can run the migration.

```sh
 DATABASE_URL=postgres://postgres@localhost:5432/socialnetwork npm run migrate up
```

This will create the `users` table in the database. We can check it with the following command.

```sh
 psql -U postgres -d socialnetwork

socialnetwork=# \d users
                                       Table "public.users"
   Column   |           Type           | Collation | Nullable |              Default              
------------+--------------------------+-----------+----------+-----------------------------------
 id         | integer                  |           | not null | nextval('users_id_seq'::regclass)
 created_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
 updated_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
 bio        | character varying(400)   |           |          | 
 username   | character varying(30)    |           | not null | 
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)
```
