---
date: 2025-04-07
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Generating and Applying a Second Migration

In [[2025-03-08_Generating-and-Writing-Migrations|Generating and Writing Migrations]], we've created a migration to add a `comments` table to our database. Now we want to add a new migration to mutate the column `contents` to `body` in the `comments` table.


First, we need to generate a new migration file. We can do this using the `npm run migrate create` command with a description of the migration.

```sh
 npm run migrate create rename contents to body

> ig@1.0.0 migrate
> node-pg-migrate create rename contents to body

Created migration -- /home/matt/Projects/ig/migrations/1743996744179_rename-contents-to-body.js
```

Then, we can open the generated migration file and add the SQL command to rename the column. Remember also add the `down` function to revert the change.

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
  // Rename the column contents to body
  pgm.sql(`
    ALTER TABLE comments
    RENAME COLUMN contents TO body;
  `)
};

/**
 * @param pgm {import('node-pg-migrate').MigrationBuilder}
 * @param run {() => void | undefined}
 * @returns {Promise<void> | void}
 */
exports.down = (pgm) => {
  // Revert the column name back to contents
  pgm.sql(`
    ALTER TABLE comments
    RENAME COLUMN body TO contents;
  `)
};
```

Now we can run the migration using the `npm run migrate up` command. This will apply all pending migrations, including the one we just created.

```sh
 DATABASE_URL=postgres://postgres@localhost:5432/socialnetwork npm run migrate up

> ig@1.0.0 migrate
> node-pg-migrate up

> Migrating files:
> - 1741411109659_table-comments
> - 1743996744179_rename-contents-to-body
### MIGRATION 1741411109659_table-comments (UP) ###

    CREATE TABLE comments (
      id SERIAL PRIMARY KEY,
      created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
      updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
      contents VARCHAR(240) NOT NULL
    );
  ;
INSERT INTO "public"."pgmigrations" (name, run_on) VALUES ('1741411109659_table-comments', NOW());


### MIGRATION 1743996744179_rename-contents-to-body (UP) ###

    ALTER TABLE comments
    RENAME COLUMN contents TO body;
  ;
INSERT INTO "public"."pgmigrations" (name, run_on) VALUES ('1743996744179_rename-contents-to-body', NOW());


Migrations complete!
```

Now we can check the database to see if the column has been renamed successfully.

```sh
 psql -U postgres -d socialnetwork
psql (17.2)
Type "help" for help.

socialnetwork=# \d comments
                                       Table "public.comments"
   Column   |           Type           | Collation | Nullable |               Default                
------------+--------------------------+-----------+----------+--------------------------------------
 id         | integer                  |           | not null | nextval('comments_id_seq'::regclass)
 created_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
 updated_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
 body       | character varying(240)   |           | not null | 
Indexes:
    "comments_pkey" PRIMARY KEY, btree (id)
```
