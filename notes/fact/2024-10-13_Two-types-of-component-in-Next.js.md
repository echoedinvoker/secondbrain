---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Two types of component in Next.js

There are two types of components in Next.js: **server component** and **client component**.

## Server component

In Next.js, a server component is a component that is executed on the server side. It has best integration with the server side and it has better performance, security and UX. By default, all components in Next.js are server components.

Because it is executed on the server side, it has some [[2024-10-13_Benefits-and-limitations-of-server-components|limitations]] also.


## Client component

In Next.js, you need to declare a component as a client component by using the `use client` keyword at the top of the file. A client component is executed on the client side and can interact with the browser's DOM. It is much more like the components in React.

Because it is executed on the client side, it has some [[2024-10-13_Benefits-and-limitations-of-client-components|limitations]] also.
