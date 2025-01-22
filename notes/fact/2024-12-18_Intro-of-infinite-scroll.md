---
date: 2024-12-18
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Intro of infinite scroll

**Infinite scroll**
- fetch new data "just in time" as user scrolls
- more efficient than fetching all data at once

**Fetch new data when...**
- user clicks a button
- user scrolls to certain point on the page

![intro-scroll.png](../assets/imgs/intro-scroll.png)

**useInfiniteQuery**
- Requires different type of API format other than pagination
- `useQuery` track current page, but `useInfiniteQuery` track next query
- next query is returned as part of data, for example:
```json
{
  "count": 10,
  "next": "http://example.com/api/v1/posts?page=2", // next query
  "previous": null,  // because here is page 1
  "results": [
    { "id": 1, "title": "Post 1" },
    { "id": 2, "title": "Post 2" },
    ...
  ]
}
```
```
