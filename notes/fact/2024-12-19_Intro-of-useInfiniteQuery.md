---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Intro of useInfiniteQuery

## Shape of useInfiniteQuery Data

*   Shape of data different than `useQuery`, `useQuery`'s data is just the resovled promise of the `queryFn`, but `useInfiniteQuery`'s data is an object with two properties
*   Object with two properties:
    *   `pages`
    *   `pageParams`
*   Every query has its own element in the `pages` array
*   `pageParams` tracks the keys of queries that have been retrieved(rarely used, won't use here)


## useInfiniteQuery Syntax

*   `pageParam` is a parameter passed to the `queryFn`

    ```javascript
    useInfiniteQuery({
        queryKey: ["sw-people"],
        queryFn: ({ pageParam = initialUrl }) => fetchUrl(pageParam)
    })
    ```

*   Current value of `pageParam` is maintained by React Query
    - which is different from `useQuery` where the current page is maintained by the component

*   `useInfiniteQuery` options

    *   `getNextPageParam: (lastPage, allPages)`
        so there are two options to get the next pageParam
        *   Updates `pageParam`
        *   Might use all of the pages of data (`allPages`)
        *   We will use just the `lastPage` of data (specifically the `next` property)

        *   next query is returned as part of the data (usually in `next` property)

```json
{
    "count": 37,
    "next": "[http://swapi.dev/api/species/?page=2](http://swapi.dev/api/species/?page=2)",
    "previous": null,
    "results": [ ... ]
}
```


## useInfiniteQuery Return Object Properties

*   `fetchNextPage`
    *   function to call when the user needs more data
*   `hasNextPage`
    *   Based on return value of `getNextPageParam`
    *   If undefined, no more data
*   `isFetchingNextPage`
    *   For displaying a loading spinner
    *   We'll see an example of when it's useful to distinguish between `isFetching` and `isFetchingNextPage`
        - `isFetching` just represents the fetching process no matter if it's the first page or the next page, and `isFetchingNextPage` is just for the next page
