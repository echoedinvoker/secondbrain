---
date: 2025-04-25
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Test Helpers

In [[2025-04-25_Programmatic-Schema-Creation|Programmatic Schema Creation]], we write codes to create random user and schema in a test file, now we need to implement the same codes for other test files.

We can copy paste but it'll create a lot of duplicate codes, so we need to create a helper file for these codes.

```sh
 tree src
src
├── app.js
├── pool.js
├── repos
│   ├── user-repo.js
│   └── utils
│       └── to-camel-case.js
├── routes
│   └── users.js
└── test
    ├── context.js # new file 
    └── routes
        ├── users.test.js # extract codes from this file to context.js
        ├── users-three.test.js
        └── users-two.test.js
```

```javascript
// src/test/context.js

const pool = require('../../pool');

const { randomBytes } = require('crypto');
const { default: migrate } = require('node-pg-migrate');
const format = require('pg-format');

class Context {
  static async bulid() {

    // copy paste codes about creating random user and schema from users.test.js to here
    
    return new Context(roleName); // return new instance of Context
  }

  constructor(roleName) {
    this.roleName = roleName;
  }
}

module.exports = Context;
```

```javascript
// src/test/routes/users.test.js

// ...

const Context = require('../context');

beforeAll(async () => {

  // move all codes inside beforeAll to context.js and replace them with below one

  const context = await Context.bulid();
  //    ^^^^^^^ we call the bulid method to create a new instance of Context
  //            we can create more method of Context class to do some useful things
  //            such as delete the user and schema after the test
});

// ...

```
