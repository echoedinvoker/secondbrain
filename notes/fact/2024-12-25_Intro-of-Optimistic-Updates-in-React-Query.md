---
date: 2024-12-25
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Intro of Optimistic Updates in React Query

## Optimistic Updates

There are two ways to update the UI in React Query:

* **update UI before response from the server**
    * you're **optimistic** that the mutation will work
* **Another options is to update the cache**
    * more complicated, but more control
        * need to cancel queries in progress so old server data won't overwrite update
        * need to save data for possible rollback
        * need to handle rollback explicitly if update fails
    * useful if you're showing the data in multiple components**

* **reference:**
    * https://tanstack.com/query/latest/docs/react/guides/optimistic-updates
 

## Optimistic Updates with React Query

We will use the first option: update the UI before response from the server. It is very easy to do this with React Query.

* **React query makes this pretty easy**
    * get mutation data with `useMutationState`
        * mutation data means the data we send to the server
        * mutation key used to identify the mutation
        * display this data on the page while mutation is pending
* **Always invalidate query after mutation is settled**
    * We always want to update the cache after the mutation is settled
        * So that if the mutation failed, data will "roll back"
            * i.e., it will be replaced with old data from server
