---
date: 2025-01-22
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# A Few More User Columns

We previously designed a simple user table as follows:

```js
Table users {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  username VARCHAR(30)
}
```


## Additional Columns to Store in the users Table

Let's check the app to see what other information needs to be saved to the table users.

![user-app-check.png](../assets/imgs/user-app-check.png)

The information in the red box needs to be saved to the users table.

```js
Table users {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  username VARCHAR(30)

  // New columns
  bio VARCHAR(400)
  avatar VARCHAR(200) // url of the avatar image
  phone VARCHAR(25) // we usually save phone numbers as strings instead of integers
                    // because we don't need to do any arithmetic operations on them
  email VARCHAR(40)
}
```


## Two More Columns that are Not Shown in the App

And there are two more columns that we need to add to the users table even though they are not shown in the app.

```js
Table users {
  id SERIAL [pk, increment]
  created_at TIMESTAMP
  updated_at TIMESTAMP
  username VARCHAR(30)
  bio VARCHAR(400)
  avatar VARCHAR(200)
  phone VARCHAR(25)
  email VARCHAR(40)

  // two more columns
  password VARCHAR(50) // we will save the hashed password
  status VARCHAR(20) // online, offline, away, etc.
}

```

About the `status` column, the selection is limited and almost never changes, so there should be a better way than just using VARCHAR to store the status. We will discuss it later.


## What About Numbers of Users?

Some people may feel that there is still some user information on the app as follows:

![what-about-numbers-of-users.png](../assets/imgs/what-about-numbers-of-users.png)

Some people may think that it would be more convenient to directly save the above values in the table users and query them, but we will not do that. We will discuss later why we do not do it this way.
