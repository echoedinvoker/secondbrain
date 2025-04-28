---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Default Foreign Key Constrains Around Deletion

When deleting a row that still has references in other tables, the default behavior of foreign key constraints is to prevent the deletion and raise an error.

We can change this behavior, check [[2025-01-01_Testing-Deletion-Constrains|Testing Deletion Constrains]]

