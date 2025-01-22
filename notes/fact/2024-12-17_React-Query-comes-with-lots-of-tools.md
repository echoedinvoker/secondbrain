---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# React Query comes with lots of tools

![tanstack-plus.png](../assets/imgs/tanstack-plus.png)

**Loading/Error states**

React Query maintains a loading state and an error state for each query. So you don't need to manage these states yourself.

**Pagination/infinite scroll**

React Query give you tools to fetch data **in pieces**, which is needed for pagination or infinite scroll.

**Prefetching**

If you can anticipate when the user will need to fetch server data, React Query also provides tools for the client to prefetch data.

**Mutations**

React Query also manages mutations or updates of data on the server.

**De-duplication of requests**

If a page makes the same request multiple times, React Query will only make the request once and share the data between the components.

**Retry on error**

If queries fail, React Query will retry the request a few times before giving up.

**Callbacks**

After a query done, you can use callbacks to trigger other actions.
