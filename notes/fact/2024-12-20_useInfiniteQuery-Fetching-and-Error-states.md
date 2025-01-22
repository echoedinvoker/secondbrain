---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# useInfiniteQuery Fetching and Error states

We want to solve the issue of [[2024-12-19_Use-React-Infinit-Scroller-with-useInfiniteQuery|this topic]]:

```jsx
// src/people/InfinitePeople.jsx

import InfiniteScroll from "react-infinite-scroller";
import { Person } from "./Person";
import { useInfiniteQuery } from "@tanstack/react-query"; // import

const baseUrl = "https://swapi-node.vercel.app";
const initialUrl = baseUrl + "/api/people/";
const fetchUrl = async (url) => {
  const response = await fetch(url);
  return response.json();
};

export function InfinitePeople() {

  const {
    data,
    isFetching,  // true when fetching data no matter has cache or not
    fetchNextPage,
    hasNextPage,
    isLoading, // true when fetching data and no cache
    isError, // true when fetching failed
    error // when fetching failed, the error message will be here
  } = useInfiniteQuery({

    queryKey: ['sw-people'],
    queryFn: ({ pageParam = initialUrl }) => fetchUrl(pageParam),
    getNextPageParam: (lastPage) => lastPage.next ? baseUrl + lastPage.next : undefined,
  })

  // we use early return to handle initial loading and fetching error status
  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error! {error.toString()}</div>

  return (
    <>
      {/* about loading more data, we add a loading indicator here */}
      {isFetching && <div className="loading">Loading...</div>}
                      {/* ^^^^^^^^^^^^^^^^^^^ this is a absolute position to top right of screen */}
      <InfiniteScroll
        hasMore={hasNextPage}
        loadMore={() => { if (!isFetching) fetchNextPage(); }}
        initialLoad={false}
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
    </>
  )
}

```
