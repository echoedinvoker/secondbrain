---
date: 2025-02-27
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Closing Aborted Transactions

This topic will demonstrate that if there is a query error in a transaction, it will cause the transaction to enter an aborted state.

![error-query-to-aborted-state.png](../assets/imgs/error-query-to-aborted-state.png)

```sql
BEGIN;
SELECT * FROM asodifjoasjlkjf; -- error query


-- OUTPUT --
BEGIN
psql:/tmp/nvim.matt/e4oT6H/instagram-query-2025-02-27-05-23-04:2: ERROR:  relation "asodifjoasjlkjf" does not exist
LINE 1: SELECT * FROM asodifjoasjlkjf;
                      ^
```

After the error, the transaction is in an aborted state. Any further queries will be ignored until the transaction is closed.

Let's try to run a query after the error.

```sql
SELECT * FROM accounts;


-- OUTPUT --
ERROR: current transaction is aborted, commands ignored until end of transaction block
```

It will be found that this correct query cannot be executed because the transaction has already been marked as aborted state.

To close the transaction, we can use the `ROLLBACK` command.

```sql
ROLLBACK;
```

Now, this transaction is closed and connection is back to main workspace. Then, we can run queries in this connection again.


