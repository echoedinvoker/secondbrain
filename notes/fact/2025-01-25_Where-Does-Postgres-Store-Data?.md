---
date: 2025-01-25
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Where Does Postgres Store Data?

```sql
SHOW data_directory;


--- OUTPUT ---
     data_directory     
------------------------
 /var/lib/postgres/data -- This is where Postgres installed and stores data.
(1 row)
```

Let's navigate to the data directory and explore its contents.

```sh
 sudo su - postgres # Switch to the postgres user because the directory is owned by postgres.
[postgres@arch ~]$ cd /var/lib/postgres/data
[postgres@arch data]$ ls
base          pg_dynshmem    pg_logical    pg_replslot   pg_stat      pg_tblspc    pg_wal                postgresql.conf
global        pg_hba.conf    pg_multixact  pg_serial     pg_stat_tmp  pg_twophase  pg_xact               postmaster.opts
pg_commit_ts  pg_ident.conf  pg_notify     pg_snapshots  pg_subtrans  PG_VERSION   postgresql.auto.conf  postmaster.pid
# The 'base' directory contains all the data for the databases.
[postgres@arch data]$ cd base/
[postgres@arch base]$ ls
1  16384  24640  25632  4  5  pgsql_tmp # But there's no database name here, only some identifiers(OIDs).
```

We can use a query to find the database names and their corresponding OIDs.

```sql
SELECT oid, datname
FROM pg_database;


--- OUTPUT ---
  oid  |  datname  
-------+-----------
     5 | postgres
 16384 | instagram -- Here we can see the database names and their OIDs.
     1 | template1
     4 | template0
 24640 | notodo
 25632 | ig
(6 rows)
```

Let's dive into the `instagram` database to see its contents.

```sh
[postgres@arch base]$ cd 16384/
[postgres@arch 16384]$ ls
112        2224       2579      2620      2755      3456_fsm  4150
113        2228       2600      2620_fsm  2756      3456_vm   4151
1247       2328       2600_fsm  2650      2757      3466      4152
1247_fsm   2336       2600_vm   2651      2830      3467      4153
1247_vm    2337       2601      2652      2831      3468      4154
1249       24576      2601_fsm  2653      2832      3501      4155
1249_fsm   24577      2601_vm   2654      2833      3502      4156
1249_vm    24577_fsm  2602      2655      2834      3503      4157
1255       24577_vm   2602_fsm  2656      2835      3534      4158
1255_fsm   24583      2602_vm   2657      2836      3541      4159
1255_vm    24583_fsm  2603      2658      2836_fsm  3541_fsm  4160
1259       24595      2603_fsm  2659      2836_vm   3541_vm   4163
1259_fsm   24596      2603_vm   2660      2837      3542      4164
1259_vm    24596_fsm  2604      2661      2838      3574      4165
13448      24596_vm   2605      2662      2838_fsm  3575      4166
13448_fsm  24602      2605_fsm  2663      2838_vm   3576      4167
13448_vm   24602_fsm  2605_vm   2664      2839      3596      4168
13451      24619      2606      2665      2840      3597      4169
13452      24620      2606_fsm  2666      2840_fsm  3598      4170
13453      24620_fsm  2606_vm   2667      2840_vm   3599      4171
13453_fsm  24620_vm   2607      2668      2841      3600      4172
13453_vm   24626      2607_fsm  2669      2995      3600_fsm  4173
13456      24626_fsm  2607_vm   2670      2996      3600_vm   4174
13457      24628      2608      2673      3079      3601      5002
13458      24628_fsm  2608_fsm  2674      3079_fsm  3601_fsm  548
13458_fsm  25543      2608_vm   2675      3079_vm   3601_vm   549
13458_vm   25553      2609      2678      3080      3602      6102
13461      25572      2609_fsm  2679      3081      3602_fsm  6104
13462      25573      2609_vm   2680      3085      3602_vm   6106
13463      25573_fsm  2610      2681      3118      3603      6110
13463_fsm  25573_vm   2610_fsm  2682      3119      3603_fsm  6111
13463_vm   25578      2610_vm   2683      3164      3603_vm   6112
13466      25578_fsm  2611      2684      3256      3604      6113
13467      25580      2612      2685      3257      3605      6116
1417       25580_fsm  2612_fsm  2686      3258      3606      6117
1418       25592      2612_vm   2687      3350      3607      6175
16389      25592_fsm  2613      2688      3351      3608      6176
16390      25592_vm   2615      2689      3379      3609      6228
16390_fsm  25596      2615_fsm  2690      3380      3712      6229
16390_vm   25598      2615_vm   2691      3381      3764      6237
16397      25598_fsm  2616      2692      3394      3764_fsm  6238
16398      25600      2616_fsm  2693      3394_fsm  3764_vm   6239
16399      25600_fsm  2616_vm   2696      3394_vm   3766      826
16401      25625      2617      2699      3395      3767      827
16402      25625_fsm  2617_fsm  2701      3429      3997      828
16402_fsm  25626      2617_vm   2702      3430      4143      pg_filenode.map
16402_vm   25627      2618      2703      3431      4144      pg_internal.init
16410      25628      2618_fsm  2704      3433      4145      PG_VERSION
16410_fsm  25628_fsm  2618_vm   2753      3439      4146
174        25628_vm   2619      2753_fsm  3440      4147
175        25629      2619_fsm  2753_vm   3455      4148
2187       25630      2619_vm   2754      3456      4149
```

There are too many identifiers above, they do not seem to represent a table, but there are also too few to represent each record. We can query pg_class to find out the meaning of these identifiers.

```sql
SELECT oid, relname
FROM pg_class;

  oid  |                    relname                     
-------+------------------------------------------------
 16389 | users_id_seq
 16397 | pg_toast_16390
 16398 | pg_toast_16390_index
 16399 | users_pkey
 24576 | comments_id_seq
 24583 | comments_pkey
 24577 | comments
 25543 | hashtags_id_seq
 25544 | hashtags  
 25549 | hashtags_pkey
 25551 | hashtags_title_key
 16390 | users
  2619 | pg_statistic
  1247 | pg_type
 16401 | posts_id_seq
 16410 | posts_pkey
 16402 | posts
 24595 | likes_id_seq
 24602 | likes_pkey
 24596 | likes
 25553 | hashtags_posts_id_seq
 25554 | hashtags_posts
 -- ...
 -- ...
 -- ...
 13584 | foreign_servers
 13588 | _pg_foreign_tables
 13593 | foreign_table_options
 13606 | user_mapping_options
 13611 | user_mappings
(449 rows)
```

We can find that these identifiers correspond to various objects, such as tables, indexes, sequences, views, functions, etc., in PostgreSQL. These objects all have corresponding OIDs, which are the identifiers mentioned above. This way, we can find the corresponding object through its OID.

For example, the `16390` file under the folder `16384` stores the all users' data in the `instagram` database. And the `16389` and the `16399` files are the sequences and primary key constraints for the `users` table, respectively. In PostgreSQL, they are individual objects with their own OIDs.
