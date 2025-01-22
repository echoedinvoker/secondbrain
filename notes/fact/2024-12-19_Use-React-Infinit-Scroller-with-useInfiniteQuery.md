---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Use React Infinit Scroller with useInfiniteQuery

In [[2024-12-19_Write-useInfiniteQuery|this topic]], we already done with useInfiniteQuery. Now, we will use `React Infinit Scroller` to render the data with properties provides by useInfiniteQuery.

Of course, you can choose other libraries or implement your own infinite scroll solution. However, `React Infinit Scroller` is a popular choice and works well with `useInfiniteQuery`.


## Intro of React Infinit Scroller

https://www.npmjs.com/package/react-infinite-scroller

`React Infinit Scroller` has some props:
- **hasMore**: A boolean value indicating whether there are more items to load. It is equivalent to `hasNextPage` in `useInfiniteQuery`.
- **loadMore**: A function to call when more items should be loaded. But we will pass an anonymous function as below:
```javascript
loadMore={() => {
  if (!isFetching) fetchNextPage();
  // ^^^^^^^^^^^^^ ^^^^^^^^^^^^^ This is the function from useInfiniteQuery
  // ^^^^^^^^^^^^^also from useInfiniteQuery, we use it to prevent repeated fetching
}}
```

And `React Infinit Scroller` will detect when to load more data by itself, we don't worry about it.

In fact, `React Infinite Scroller` is not responsible for rendering data, it is only responsible for determining when to load more data. So we need its children part to use the data from useInfiniteQuery to write the logic for rendering data.


## Coding

```jsx
// src/people/InfinitePeople.jsx

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

  // use React Infinit Scroller to detect when to load more data
  return <InfiniteScroll
    hasMore={hasNextPage}
    loadMore={() => { if (!isFetching) fetchNextPage(); }}
  >
    {/* write render logic here on our own */}
    {data.pages.map((page) => page.results.map((person) => (
      <Person
        key={person.fields.name}
        name={person.fields.name}
        hairColor={person.fields.hair_color}
        eyeColor={person.fields.eye_color}
      />
    )))}

  </InfiniteScroll >
}

```

## Test

```bash
npm run dev

# Uncaught TypeError: Cannot read properties of undefined (reading 'pages')

```
We'll fix it later...

