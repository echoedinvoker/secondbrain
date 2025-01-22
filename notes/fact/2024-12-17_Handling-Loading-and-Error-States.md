---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Handling Loading and Error States

In [[2024-12-17_Creating-Queries-with-useQuery|this topic]], we simply use `!data` to handle the situation that fetching data is not completed or failed.

But `useQuery` provides more properties to help us handle different situations more accurately.

```jsx
// src/Posts.jsx

import { useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
import { PostDetail } from "./PostDetail";
import { useQuery } from "@tanstack/react-query";
const maxPostPage = 10;

export function Posts() {
  const [currentPage, setCurrentPage] = useState(0);
  const [selectedPost, setSelectedPost] = useState(null);

  const { data, isLoading, isError, error } = useQuery({
             // ^^^^^^^^^^^^^^^^^^^^^^^^^ destructuring three other properties which used to indicate fetching status or result
    queryKey: ["posts"],
    queryFn: fetchPosts,
  })

  // replace !data with isLoading, which is more accurate
  if (isLoading) return <p>Loading...</p>;

  // there is another case that still no data, it's retrieving data error situation
  // we can use `isError` to handle this case
  if (isError) return <p>Opps! Something went wrong: {error.toString()}</p>;
                                                   // ^^^^^ usually is null, only have value when error occurs
    // you may find that loading time is longer than before,
    // because React Query will automatically refetch data three times when error occurs,
    // when refetching, it will still show loading status

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
        <button disabled onClick={() => {}}>
          Previous page
        </button>
        <span>Page {currentPage + 1}</span>
        <button disabled onClick={() => {}}>
          Next page
        </button>
      </div>
      <hr />
      {selectedPost && <PostDetail post={selectedPost} />}
    </>
  );
}

```

