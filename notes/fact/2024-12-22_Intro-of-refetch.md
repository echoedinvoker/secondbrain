---
date: 2024-12-22
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Intro of refetch


## Re-fetching! Why? When?

* Re-fetch ensures stale data gets updated from server
    * Seen when we leave the page and refocus

- Stale queries are re-fetched automatically in the background when:
    * New instances of the query mount
    * Every time a react component (that has a `useQuery` call) mounts
    * The window is refocused
    * The network is reconnected
    * configured `refetchInterval` has expired
        * Automatic polling


## Re-fetching! How?

* Control with global or query-specific options:
    * `refetchOnMount`, `refetchOnWindowFocus`, `refetchOnReconnect`, `refetchInterval`
    - they are all set to `true` by default, except `refetchInterval`
        - so React Query is aggressive to re-fetch by default
    - only `refetchInterval` is number (in milliseconds), others are boolean
* Or, imperatively: `refetch` function in `useQuery` return object
* reference: https://tanstack.com/query/latest/docs/react/guides/important-defaults


## Suppressing Re-Fetch

* How?
    * Increase stale time
    * turn off `refetchOnMount` / `refetchOnWindowFocus` / `refetchOnReconnect`
* Only for very rarely changed, not mission-critical data
    * treatments or staff (definitely not appointments!)
* Ask: is it worth it?
* Remember Tanner Linsley quote:
![linsley-quote.png](../assets/imgs/linsley-quote.png)
