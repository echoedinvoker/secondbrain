---
date: 2024-12-23
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Intro of JWT auth of Lazy Days app


## JWT Authentication

This app uses `JWT (JSON Web Token)` authentication. (Because lecturer don't want students need to sign up with the 3rd party service)

The user inputs their account password and sends it to the server to verify if there is a match in the database. If there is a match, the server will send a token back to the user, and the user's username or ID will be encrypted within this token.

When an endpoint requiring logged in permission is requested, the frontend will place the token in the request header, and the server will decrypt the token and verify if this user exists in the database. If so, it will return the corresponding response.

Therefore, JWT is a stateless authentication method that does not require storing sessions on the server side, nor does it require storing tokens on the server side. It only needs to store tokens on the client side, and there is a secret string in the server environment for encryption and decryption, making it easier for the server to scale.

In this project, the token is carried in the user object and sent back to the client side from the server side.


## Persist Token in the frontend

Whenever the browser refreshes the page, the front-end app will reload, causing the token in memory to disappear. Therefore, we need a way to persist the token in order to avoid having to log in again every time the page reloads.

There are many ways to persist tokens, this project uses local storage to persist tokens.


## Already set up JWT on the client side and server side

Because this is a course on React Query, the JWT part itself has already been set up. We will focus on writing the interaction between React Query and Auth. However, it is still important to understand how the JWT in this project works.
