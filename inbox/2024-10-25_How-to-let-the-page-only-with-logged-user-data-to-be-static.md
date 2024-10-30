---
date: 2024-10-25
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# How to let the page only with logged user data to be static

It is almost impossible to make a page with user-specific data static. But you can cache dynamic page also (It takes storage of server).


**The reason**

1. You must get session for authentication, but `auth` use header/cookie which is not available in static page.
2. So, you should use client component to get session by `useSession` hook, and then [[2024-10-25_It-said-I-use-aysnc-function-in-client-component,-but-I-can't-find-out|you meet a BIG limitation]].

