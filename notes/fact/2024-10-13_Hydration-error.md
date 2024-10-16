---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Hydration error

**What is a hydration error?**

It happens during the [[2024-10-13_Hydration|hydration]] process, there are three common causes of hydration errors:
1. when the SSR (Server-Side Rendering) and the CSR (Client-Side Rendering) are out of sync.
2. Using `window` or `document`, which are not available during the server-side rendering.
3. Using dynamic contents such as date, time, or random numbers.


**How to fix the error?**

1. Check if any usage of client-side APIs in the server-side rendering.
2. Wrap the client-side component with `<Suspense>`.

