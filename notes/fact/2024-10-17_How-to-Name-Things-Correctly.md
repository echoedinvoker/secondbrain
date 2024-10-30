---
date: 2024-10-17
type: fact
aliases:
  -
hubs:
  - "[[CleanCode]]"
---

# How to Name Things Correctly

## Variables & Constants

**Data containers**

e.g. user input data, validation results, a list of products.

**Use nouns or short phrases with adjective**
```js
const userData = { ... }
const isValid = ...
```

## Functions / Methods

**Commands or calculated values**

e.g. send data to server, check if user input is valid.

**Use verbs or short phrases with adjective**
```js
sendData()
inputIsValid()
```

## Classes

**Use classes to create 'things'**

e.g. a user, a product, a http request body.

**Use nouns or short phrases with nouns**
```js
class User { ... }
class RequestBody { ... }
```
