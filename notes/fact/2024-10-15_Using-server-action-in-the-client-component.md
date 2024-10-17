---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Using server action in the client component

There are two options to use a server action in the client component:

1. Use the form action property to call a server action. And bind server action to the required states.
[[2024-10-15_Using-form-action-to-call-server-action-in-the-client-component|Using form action to call server action in the client component]]

2. Just call the server action in traditional way. And wrap it with the startTransition to avoid navigation before data is fetched.
[[2024-10-15_Calling-server-action-in-traditional-way-in-the-client-component|Calling server action in traditional way in the client component]]

