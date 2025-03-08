---
date: 2025-02-27
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Transaction Cleanup on Crash

![crash-in-the-middle-of-withdraw.png](../assets/imgs/crash-in-the-middle-of-withdraw.png)

For this topic, we need to demonstrate the situation of a crash during the withdrawal process.

Assume all balances are 100 at the beginning.

```sql
BEGIN;
UPDATE accounts
SET balance = balance - 50
WHERE name = 'Alyson';

-- Crash here, so only the above statement is executed
```

When connection with transaction crashes, the workspace will be removed automatically. So the main workspace won't be affected by any operation in the crashed workspace.

![crash-remove-workspace.png](../assets/imgs/crash-remove-workspace.png)


Let's open another terminal and check the balance of the account.

```sql
SELECT *
FROM accounts;

-- OUTPUT --
 id |  name  | balance 
----+--------+---------
  1 | Gia    |     100
  2 | Alyson |     100 -- rollback to 100
(2 rows)
