---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# React Query Manages Data

It means how React Query indicate when to update cache with new data from sever, there are two different ways:

**imperative**: 

You can manually trigger a query to refetch or update the cache, we call it `invalidate` data.

**declarative**:

`Configure` how & when to trigger a re-fecth to update the cache, it's more like a `watch` mechanism.

