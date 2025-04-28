---
date: 2025-04-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Setup server and APIs

```sh
 tree
.
├── migrations
├── node_modules
├── package.json
├── package-lock.json
└── src # new directory
    └── app.js # new file to start the server
```

```js
const express = require('express');

module.exports = () => {  // here we export a function that returns the app
  const app = express();  // this is for testing purposes

  app.use(express.json());

  return app;
};
```

Then, we create a route and some endpoints (only signatures for now)

```sh
 tree
.
├── migrations
├── node_modules
├── package.json
├── package-lock.json
└── src
    ├── app.js
    └── routes # new directory
        └── users.js # new file for the users route
```

```js
const express = require('express');

const router = express.Router();

router.get('/users', async (req, res) => {});

router.get('/users/:id', async (req, res) => {});

router.post('/users', async (req, res) => {});

router.put('/users/:id', async (req, res) => {});

router.delete('/users/:id', async (req, res) => {});

module.exports = router;

```

Then, we associate the route with the app in `app.js`:

```js
const express = require('express');
const usersRouter = require('./routes/users'); // import the users route

module.exports = () => {
  const app = express();

  app.use(express.json());
  app.use(usersRouter); // use the users route

  return app;
};

```
