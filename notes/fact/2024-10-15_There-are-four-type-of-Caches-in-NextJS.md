---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# There are four type of Caches in NextJS

Next implements caching in several locations.
Can lead to unexpected behavior

| Type | Description |
|------|-------------|
| Data Cache | Responses from requests made with 'fetch' are stored and used across requests. |
| Router Cache | 'Soft' navigation between routes are cached in the browser and reused when a user revisits a page. |
| Request Memoization | Make two or more 'GET' requests with 'fetch' during a user's request to your server? Only one 'GET' is actually executed. |
| Full Route Cache | At build time, Next decides if your route is static or dynamic. If it is static, the page is rendered and the result is stored. In production, users are given this pre-rendered result. |

>Data Cache, Router Cache and Request Memoization work in development and production. [[2024-10-15_Full-Route-Cache|Full Route Cache]] only works in production.
