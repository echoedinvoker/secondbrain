---
date: 2025-04-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Creating a Posts Table

We want to implement the first step in [[2025-04-07_Properly-Running-Data-and-Schema-Migrations|Properly Running Data and Schema Migrations]]

So, let's create a new migration file:

```sh
 npm run migrate create add new table posts

> ig@1.0.0 migrate
> node-pg-migrate create add new table posts

Created migration -- /home/matt/Projects/ig/migrations/1744010673416_add-new-table-posts.js
```

Then, we can open the migration file and add SQL to create the posts table for the `up` migration and remove it for the `down` migration.

```js
/**
 * @type {import('node-pg-migrate').ColumnDefinitions | undefined}
 */
exports.shorthands = undefined;

/**
 * @param pgm {import('node-pg-migrate').MigrationBuilder}
 * @param run {() => void | undefined}
 * @returns {Promise<void> | void}
 */
exports.up = (pgm) => {
  // logic to create the posts table
  pgm.sql(`
    CREATE TABLE posts (
      id SERIAL PRIMARY KEY,
      url VARCHAR(300),
      lat NUMERIC,
      lng NUMERIC
    );
  `)
};

/**
 * @param pgm {import('node-pg-migrate').MigrationBuilder}
 * @param run {() => void | undefined}
 * @returns {Promise<void> | void}
 */
exports.down = (pgm) => {
  // logic to drop the posts table
  pgm.sql(`
    DROP TABLE posts;
  `)
};
```

Now, we can run the migration to create the posts table:

```sh
 DATABASE_URL=postgres://postgres@localhost:5432/socialnetwork npm run migrate up

> ig@1.0.0 migrate
> node-pg-migrate up

> Migrating files:
> - 1744010673416_add-new-table-posts
### MIGRATION 1744010673416_add-new-table-posts (UP) ###

    CREATE TABLE posts (
      id SERIAL PRIMARY KEY,
      url VARCHAR(300),
      lat NUMERIC,
      lng NUMERIC
    );
  ;
INSERT INTO "public"."pgmigrations" (name, run_on) VALUES ('1744010673416_add-ne
w-table-posts', NOW());


Migrations complete!
```

Now, the first step is complete. We have a posts table with initial columns in the database.



