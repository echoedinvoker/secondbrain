---
date: 2025-01-15
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Config based Schema Designer

In [[2025-01-15_Using-a-SQL-Design-Tool|this topic]], we learned how to use a SQL schema design tool which provides lots of drawing tools. But, there is another type of SQL schema design tool which is based on a configuration file. 

[dbdiagram.io](https://dbdiagram.io/home) is a config-based SQL schema design tool. Let's see how it works.


![go-to-dbdiagram-io.png](../assets/imgs/go-to-dbdiagram-io.png)

Clicking on `Create your diagram` directly takes you to the editing page without logging in, and there are some example codes for reference.

![dbdiagram-io-sample.png](../assets/imgs/dbdiagram-io-sample.png)


We can see that the codes on the left are similar to SQL but not exactly the same. It is a custom language, but not difficult to understand. After clearing the codes on the left, we can rewrite the schema structure we want.

```js
Table users {
  id SERIAL [pk, increment]
  // ^^^^^^ ^^^^^^^^^^^^^^^ use [ ] to add column attributes
  // ^^^^^^ type in dbdiagram.io is just a text, not a real type in SQL, so we need to use [ ] to add column attributes
  username varchar
}

Table comments {
  id SERIAL [pk, increment]
  contents VARCHAR
  user_id INTEGER [ref: > users.id]
  //      ^^^^^^^ ^^^^^^^^^^^^^^^^^ reference to users.id, means it's a foreign key
  //      ^^^^^^^ equal to `integer`
  post_id int [ref: > posts.id]
}

Table posts {
  id SERIAL [pk, increment]
  title VARCHAR
}
```

![dbdiagram-grow-graph.png](../assets/imgs/dbdiagram-grow-graph.png)

Using configuration files has many advantages. We can save codes in Git for version control and share them with others for collaboration.


