---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Creating Queries with useQuery

In [[2024-12-17_Setup-React-Query|this topic]], we have already setup React Query in our project. Now, we will create a query to fetch posts by using `useQuery` hook.

```jsx
// src/Posts.jsx 

import { useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
      // ^^^^^^^^^^ this is function to fetch posts from the server, but we don't use it directly,
      //            instead we use it in the useQuery hook.
import { PostDetail } from "./PostDetail";
import { useQuery } from "@tanstack/react-query";  // hook from react-query client
const maxPostPage = 10;

export function Posts() {
  const [currentPage, setCurrentPage] = useState(0);
  const [selectedPost, setSelectedPost] = useState(null);

  // useQuery hook to fetch posts
  const { data } = useQuery({
       // ^^^^ resolved result of `fetchPosts` function will be stored in `data` variable
    queryKey: ["posts"], // value of query key is always an array
    queryFn: fetchPosts, // the function to fetch posts
  })

  // `fetchPosts` is async function, so it takes some time to fetch posts from the server.
  // `data` will be `undefined` until the data is fetched from the server.
  // so we need to handle this case, or `data.map` below will throw an error.
  if (!data) return <p>Loading...</p>;

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

