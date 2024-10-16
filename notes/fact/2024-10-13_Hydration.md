---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Hydration

It is the process to turn the SSR static HTML into a dynamic DOM on the client side.


**static to dynamic**

It adds event listeners and states to SSR static HTML to make it dynamic.


**Why is it needed?**

1. Speed up the initial load time. (Becuase init HTML is already rendered on the server)

2. SEO friendly. (Because the search engine can see the initial HTML)

3. Still have the dynamic behavior of the client-side app after the hydration.
