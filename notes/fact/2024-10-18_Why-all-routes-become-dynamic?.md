---
date: 2024-10-18
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# 2024-10-18_Why-all-routes-become-dynamic?

**We prefer static routes**

We prefer to use static routes because they provide better performance. We should regularly use `npm run build` to check the status of the routes.


**When we got dynamic routes unexpectedly**

When we got dynamic routes unexpectedly, we need to check the following list to find out the reason:
[[2024-10-15_What-makes-a-route-DYNAMIC|What makes a route DYNAMIC]]


**If we can't find the reason from the above list**

This may be because:

1. When considering a route, the layout.tsx that affects this route must also be taken into consideration. The problem may lie in layout.tsx.

2. When considering a route, all child components that affect this route must also be taken into consideration. The problem may lie in the child components.

3. Some library methods already break the list above. For example:

```ts
const session = await auth()  // auth uses cookies, so it makes the route dynamic

```
[[2024-10-18_Solve-auth-method-make-route-dynamic-issue|Solve auth method make route dynamic issue|Here]] is the way to solve the above issue.


