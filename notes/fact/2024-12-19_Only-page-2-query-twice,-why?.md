---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Only page 2 query twice, why?

When we were testing in [[2024-12-19_Use-React-Infinit-Scroller-with-useInfiniteQuery|this topic]], we found that the second page of data is fetched twice.

![page-2-twice.png](../assets/imgs/page-2-twice.png)

This is because `InfiniteScroll` normally fetches the first set of data using `loadMore` also, but when used with `useInfiniteQuery`, the first set of data is fetched using the `queryFn` of `useInfiniteQuery` itself, only the subsequent data is fetched using its `fetchNextPage`, resulting in this situation.

`InfiniteScroll` provides the property `initialLoad` to control whether the first set of data should also be fetched using `loadMore`. The default value is `true`, setting it to `false` will solve this issue.

```jsx
// src/people/InfinitePeople.jsx

import InfiniteScroll from "react-infinite-scroller";
import { Person } from "./Person";
import { useInfiniteQuery } from "@tanstack/react-query";

const baseUrl = "https://swapi-node.vercel.app";
const initialUrl = baseUrl + "/api/people/";
const fetchUrl = async (url) => {
  const response = await fetch(url);
  return response.json();
};

export function InfinitePeople() {

  const {
    data,
    isFetching,
    fetchNextPage,
    hasNextPage
  } = useInfiniteQuery({

    queryKey: ['sw-people'],
    queryFn: ({ pageParam = initialUrl }) => fetchUrl(pageParam),
    getNextPageParam: (lastPage) => lastPage.next ? baseUrl + lastPage.next : undefined,
  })

  if (!data) return

  return <InfiniteScroll
    hasMore={hasNextPage}
    loadMore={() => { if (!isFetching) fetchNextPage(); }}
    initialLoad={false}  // <--- Add, prevents the first set of data from being fetched using loadMore
  >
    {
      data.pages.map((page) => page.results.map((person) => (
        <Person
          key={person.fields.name}
          name={person.fields.name}
          hairColor={person.fields.hair_color}
          eyeColor={person.fields.eye_color}
        />
      )))
    }

  </InfiniteScroll >
}
```

![fix-2-twice.png](../assets/imgs/fix-2-twice.png)
