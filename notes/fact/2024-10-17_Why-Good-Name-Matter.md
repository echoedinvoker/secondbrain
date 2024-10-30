---
date: 2024-10-17
type: fact
aliases:
  -
hubs:
  - "[[CleanCode]]"
---

# Why Good Name Matter

Every name should be meaningful and should describe what it does. It should be easy to understand and should not require any additional explanation or check the implementation to understand what it does.

**Bad Example:**

```js
const us = new MainEntity();  // what us store?
us.process();              // what process do?

if (login) {  // login or logout?
  //...
}
```

**Good Example:**

```js
const user = new User();  // store user data
user.save();  // save user data

if (isLoggedIn) {  // check if user logged in
  //...
}
```

