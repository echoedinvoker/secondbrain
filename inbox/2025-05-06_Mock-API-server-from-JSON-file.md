---
date: 2025-05-06
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Mock API server from JSON file

We can use the library `json-server` to quickly create a mock API server based on a json file for development and testing purposes.

```sh
$ npm install --save-dev json-server@^0.17.4
#                                   ^^^^^^^^ install a specific version because recent versions
#                                            remove a feature we need (response delay)
```

Then, create a script in your `package.json` file to start the server:

```json
{
  ...
  "scripts": {
    ...
    "server": "json-server --watch db.json --delay=3000 --port=3001"
  },
  ...
}
```

`--delay` is used to simulate a slow server response.

And because we set the `--watch db.json` option, we need to create a `db.json` file in the root of our project.

```json
{
  "events": [ ... ],
  "bookings": [ ... ]
}
```

Now, we can start the server:

```sh
$ npm run server

> event-booking@0.0.0 server
> json-server --watch db.json --delay=3000 --port=3001


  \{^_^}/ hi!

  Loading db.json
  Done

  Resources
  http://localhost:3001/events
  http://localhost:3001/bookings

  Home
  http://localhost:3001

  Type s + enter at any time to create a snapshot of the database
  Watching...
```
You can find that it automatically converts the keys of the JSON into endpoints in the API server, such as the keys `events` and `bookings` will become the endpoints `/events` and `/bookings`.
