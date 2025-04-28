---
date: 2025-03-25
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Status Codes Overview

## What are status codes?

- An HTTP status code is used to help the Client (the user or system submitting data to the server) to understand what happened on the server side application.

- Status codes are international standard on how a Client/Server should handle the result of a request.

-It allows everyone who sends a request to know if their submission was successful or not.


## Status Codes:

- **1xx**: Information responses: Request Processing
- **2xx**: Success: Request Successfully complete
- **3xx**: Redirection: Further action must be complete
- **4xx**: Client Error: An error was caused by the Client
- **5xx**: Server Error: An error occurred on the server


## 2xx Successful status codes

- **200: OK**: Standard response for a successful request. Commonly used for successful GET requests when data is being returned.
- **201: Created**: The request has been successful, creating a new resource. Used when a POST creates an entity.
- **204: No Content**: The request has been successful, did not create an entity nor return anything. Commonly used with PUT requests.


## 4xx Client Error status codes

- **400: Bad Request**: Cannot process request due to client error. Commonly used for invalid request methods.
- **401: Unauthorized**: Client does not have valid authentication for tartget resource.
- **404: Not Found**: The client requested resource can not be found.
- **422: Unprocessable Entity**: Semantic Errors in Client request.


## 5xx Server Error status codes

- **500: Internal Server Error**: Generic Error Message, when an unexpected issue on the server happened.

