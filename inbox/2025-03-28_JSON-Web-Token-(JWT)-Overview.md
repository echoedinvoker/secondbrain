---
date: 2025-03-28
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# JSON Web Token (JWT) Overview

## What is a JSON Web Token (JWT)?

- JWT is a self-contained way to securely transmit data and information between two using a JSON object.
- JWT can be trusted because each JWT can be digitally signed, which in return allows the server to know if the JWT has been changed at all.
- JWT is not an authentication method like basic and digest, but an *authorization* method in which allows the client and server to maintain a relationship without having to log in each request.
- JWT is a great way for information to be exchanged between the server and client. Once the user logs in, the server will return a JWT, which is a string of characters that can, when decoded, shows information about the client and user. Each time a request comes from the client, after a successful authentication, the client will send the JWT and the server will validate the token each request.


## JWT Structure

- A JWT is created of three seperate parts separated by dots (.) which include:
  - Header
  - Payload
  - Signature

### Header
- A JWT header typically consists of two parts:
  - (alg) The algorithm for signing
  - (typ) The specific type of token
- The JWT header is then encoded using Base64 to create the first part of JWT.

### Payload
- A JWT payload consists of the data of the user or additional information.
- THe JWT payload is then encoded using Base64 to create the second part of JWT
- The payload contains claims, there are three types of claims:
  - Registered
  - Public
  - Private
#### Registered Claims
- Registered claims are claims that are predefined, recommended but not mandatory.
- The top three registered claims include:
  - iss (issuer): this claim identifies the principal that issued the JWT.
  - sub (subject): It holds the statement of the subject of the JWT. The subject value must be scoped either locally or globally unique. Think of a subject as an ID for the JWT.
  - exp (expiration time): It is when the JWT expires. This claim make suree that the current date and time is before the expiration date and time of the token. Expiration is not mandatory but extremely useful. One thing you never want is a token that never expires. This is because if your token never expires, then anyone who has a JWT will be authorized by the server. You will always want to make sure there is some type of default expiration date attached to the JWT.

### JWT Signature
- The JWT signature is created by using the algorithm specified in the header to hash out the encoded header, encoded payload with a secret.
- The secret can be anything, but is saved somewhere on the server that the client does not have access to.
- The signature is the third part and final part of the JWT.

## Example

You can use [jwt.io](https://jwt.io/) to create a JWT. Here is an example of a JWT:

### Decoded
**Header**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
**Payload**
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
**Signature**
```json
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  your-secret
)
```

### Encoded
```json
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```
If any of the parts are changed, the signature will not match and the JWT will be invalid.
