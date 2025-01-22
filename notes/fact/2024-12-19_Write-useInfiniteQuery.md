---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Write useInfiniteQuery

In this topic, we focus on writing out the useInfiniteQuery, without using the properties it provides yet.

```jsx
// src/people/InfinitePeople.jsx 

import InfiniteScroll from "react-infinite-scroller";
import { Person } from "./Person";
import { useInfiniteQuery } from "@tanstack/react-query"; // import

const baseUrl = "https://swapi-node.vercel.app";
const initialUrl = baseUrl + "/api/people/"; // for default value of pageParam, used in queryFn for the first query

const fetchUrl = async (url) => {
  const response = await fetch(url);
  return response.json();
};

export function InfinitePeople() {

  // start to write useInfiniteQuery here...
  const {
    data,  // includes pages and pageParam properties, pages is an array data, each query data will be inserted into pages array
    fetchNextPage, // fn to fetch next page data with latest pageParam
    hasNextPage // boolean, if pageParam is `undefined`, hasNextPage will be `false`
  } = useInfiniteQuery({

    queryKey: ['sw-people'],
    queryFn: ({ pageParam = initialUrl }) => fetchUrl(pageParam),
            //  ^^^^^^^^^^^^^^^^^^^^^^ only the first query will use initialUrl, and the next queries will use the pageParam returned by getNextPageParam
    getNextPageParam: (lastPage) => lastPage.next ? baseUrl + lastPage.next : undefined,
                    // ^^^^^^^^     ^^^^^^^^^^^^^^^^^^^^^ in our case, the url of next page is stored in `next` property of the last query data
                    // ^^^^^^^^                           and `hasNextPage` only work on `undefined`, so we need to use `|| undefined` to conver other falthy values to `undefined`
                    // ^^^^^^^^
                    // ^^^^^^^^first argument is the last query data, there is the second argument but we don't need it in our case
                      
  })

  return <InfiniteScroll />;
}


```
