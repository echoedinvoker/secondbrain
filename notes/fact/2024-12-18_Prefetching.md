---
date: 2024-12-18
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Prefetching


## Introduction

- Adds data to cache **imperatively** (by method of queryClient), before it is needed.
- Added data will be stale right after it is fetched by default stale time 0. But it's still useful to remove loading status. (as long as cache hasn't expired!)
- Prefetching can be used for any anticipated data, like hover cards, infinite scroll, etc. Not just for pages.


## Remove loading status of pagination

In [[2024-12-17_Pagination|this topic]], we found that there is a loading status when switching pages. We can use prefetching to remove this loading status and make the transition smoother:

```jsx
// src/Posts.jsx

import { useEffect, useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
import { PostDetail } from "./PostDetail";
import { useQuery, useQueryClient } from "@tanstack/react-query";
                // ^^^^^^^^^^^^^^
const maxPostPage = 10;

export function Posts() {
  const [currentPage, setCurrentPage] = useState(1);
  const [selectedPost, setSelectedPost] = useState(null);

  const queryClient = useQueryClient()  // get queryClient instance which we created in App.jsx

  // we track the currentPage and prefetch the next page whenever currentPage changes
  useEffect(() => {
    if (currentPage >= maxPostPage) return;
    const nextPage = currentPage + 1;
    queryClient.prefetchQuery({
      queryKey: ["posts", nextPage],
                // ^^^^^^^^^^^^^^^^^^^ this should be match the key of useQuery
                //                     when user navigates to the next page
                      
      queryFn: () => fetchPosts(nextPage),
    })
  }, [currentPage, queryClient])

  const { data, isLoading, isError, error } = useQuery({
    queryKey: ["posts", currentPage],
    queryFn: () => fetchPosts(currentPage),
    staleTime: 2000, // prefetch data's stale time is still 0
  })                 // the settings of prefetch and useQuery are separated

  if (isLoading) return <p>Loading...</p>;
  if (isError) return <p>Opps! Something went wrong: {error.toString()}</p>;

  return (
    <>
      <ul>
        {data.map((post) => (
          <li
            key={post.id}
            className="post-title"
            onClick={() => setSelectedPost(post)}
          >
            {post.title}
          </li>
        ))}
      </ul>
      <div className="pages">
        <button
          disabled={currentPage <= 1}
          onClick={() => {
            setCurrentPage((prevPage) => prevPage - 1)
          }}>
          Previous page
        </button>
        <span>Page {currentPage}</span>
        <button
          disabled={currentPage >= maxPostPage}
          onClick={() => {
            setCurrentPage((prevPage) => prevPage + 1); // somebody would add prefetch here, it's not good idea because state update is async, we can't be sure if the state is updated before prefetch
          }}>                                           
          Next page
        </button>
      </div>
      <hr />
      {selectedPost && <PostDetail post={selectedPost} />}
    </>
  );
}

```

![result-prefetch.png](../assets/imgs/result-prefetch.png)


