---
date: 2025-04-21
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# relationships

## Three types of relationships

There are three types of relationships between resources:

### one-to-many

For example, a *user* can have many *posts*, users and posts are two different resources.

In this case, users and posts have a *one-to-many* relationship.

We can say that a user can own *many* posts but a post can only belong to *one* user.

We use [[2025-04-21_Primary-key|primary]] and [[2025-04-21_Foreign-key|foreign key]] to [[2025-04-21_establish-a-one-to-many-relationship|establish this relationship]]

### one-to-one

For example, a *user* can have only one *profile*, users and profiles are two different resources.

In this case, users and profiles have a *one-to-one* relationship.

We can say that a user can own only *one* profile and a profile can only belong to *one* user.

### many-to-many

For example, a *user* can have many *roles* and a *role* can have many *users*, users and roles are two different resources.

In this case, users and roles have a *many-to-many* relationship.

We can say that a user can own *many* roles and a role can have *many* users.
