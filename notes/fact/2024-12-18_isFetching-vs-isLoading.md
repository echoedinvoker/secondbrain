---
date: 2024-12-18
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# isFetching vs isLoading

## Introduction

**isFetching**

- The async query function hasn't yet resolved.

**isLoading**

- No cached data, plus isFetching.

So `isLoading` is subset of `isFetching`.

![isFetching-isLoading.png](../assets/imgs/isFetching-isLoading.png)


## isFetching Use Case

In [[2024-12-18_Prefetching|this topic]], we use prefetching to remove the loading status when the user navigates to a page. But we still want to indicate that the data is being fetched. So we use `isFetching` to show a indicator.

```jsx
// src/Posts.jsx

import { useEffect, useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
import { PostDetail } from "./PostDetail";
import { useQuery, useQueryClient } from "@tanstack/react-query";
const maxPostPage = 10;

export function Posts() {
  const [currentPage, setCurrentPage] = useState(1);
  const [selectedPost, setSelectedPost] = useState(null);

  const queryClient = useQueryClient()

  useEffect(() => {
    if (currentPage >= maxPostPage) return;
    queryClient.prefetchQuery({
      queryKey: ["posts", currentPage + 1],
      queryFn: () => fetchPosts(currentPage + 1),
    })
  }, [currentPage, queryClient])

  const { data, isLoading, isError, error, isFetching } = useQuery({
    //                                     ^^^^^^^^^^
    queryKey: ["posts", currentPage],
    queryFn: () => fetchPosts(currentPage),
    staleTime: 2000,
  })

  if (isLoading) return <p>Loading...</p>;
  if (isError) return <p>Opps! Something went wrong: {error.toString()}</p>;

  return (
    <>
      {isFetching && <p>Updating...</p>} {/* instead of early return like isLoading case, we choose to show a indicator */}
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
            setCurrentPage((prevPage) => prevPage + 1);
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

![isFetching-case.png](../assets/imgs/isFetching-case.png)

