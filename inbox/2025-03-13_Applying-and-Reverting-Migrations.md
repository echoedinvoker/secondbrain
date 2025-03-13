---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Applying and Reverting Migrations

In the previous topic, we have already created a migration file. Now we will see how to apply this migration file to the database.


```sh
 DATABASE_URL=postgres://postgres@localhost:5432/socialnetwork npm run migrate up
# ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^
# we use DATABASE_URL to tell the migration tool                command to apply the migration file
# which database to connect to

> ig@1.0.0 migrate
> node-pg-migrate up

> Migrating files:
> - 1741411109659_table-comments
### MIGRATION 1741411109659_table-comments (UP) ###

    CREATE TABLE comments (
      id SERIAL PRIMARY KEY,
      created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
      updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
      contents VARCHAR(240) NOT NULL
    );
  ;
INSERT INTO "public"."pgmigrations" (name, run_on) VALUES ('1741411109659_table-
comments', NOW());


Migrations complete!

```

Then, we can check if the table has been created in the database:

```sh
 psql -U postgres -d socialnetwork
psql (17.2)
Type "help" for help.

socialnetwork=# \dt
            List of relations
 Schema |     Name     | Type  |  Owner   
--------+--------------+-------+----------
 public | comments     | table | postgres  # this is the table we created in the migration file
 public | pgmigrations | table | postgres  # and node-pg-migrate module also created this table to track the migrations
(2 rows)

socialnetwork=# \d comments # check the structure of the `comments` table if it's created correctly
                                       Table "public.comments"
   Column   |           Type           | Collation | Nullable |               Default                
------------+--------------------------+-----------+----------+--------------------------------------
 id         | integer                  |           | not null | nextval('comments_id_seq'::regclass)
 created_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
 updated_at | timestamp with time zone |           |          | CURRENT_TIMESTAMP
 contents   | character varying(240)   |           | not null | 
Indexes:
    "comments_pkey" PRIMARY KEY, btree (id)

# check the `pgmigrations` table to see if the migration file is applied to the database
socialnetwork=# SELECT * FROM pgmigrations;
 id |             name             |           run_on           
----+------------------------------+----------------------------
  1 | 1741411109659_table-comments | 2025-03-13 03:35:22.177773 # it records the migration file and when it's applied
(1 row)                                                         # so it can prevent the same migration file from being applied again
```

Assuming we don't want this migration anymore, we can use `npm run migrate down` to go back to the most recent migration.

```sh
 DATABASE_URL=postgres://postgres@localhost:5432/socialnetwork npm run migrate down
#                                                                               ^^^^ instead of `up`, we use `down` to go back to the most recent migration

> ig@1.0.0 migrate
> node-pg-migrate down

> Migrating files:
> - 1741411109659_table-comments
### MIGRATION 1741411109659_table-comments (DOWN) ###

    DROP TABLE comments;
  ;
DELETE FROM "public"."pgmigrations" WHERE name='1741411109659_table-comments';


Migrations complete!

```

Let's check the database again to see if the `comments` table is removed.

```sh
 psql -U postgres -d socialnetwork
psql (17.2)
Type "help" for help.

socialnetwork=# \dt
            List of relations
 Schema |     Name     | Type  |  Owner   
--------+--------------+-------+----------
 public | pgmigrations | table | postgres # the `comments` table is removed
(1 row)

socialnetwork=# SELECT * FROM pgmigrations;
 id | name | run_on 
----+------+--------    # the record of applying the migration file is also removed
(0 rows)

```
