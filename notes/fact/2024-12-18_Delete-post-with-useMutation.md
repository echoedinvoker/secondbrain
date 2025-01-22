---
date: 2024-12-18
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Delete post with useMutation

Continuing from [[2024-12-18_Mutations-intro|mutations intro]], we want to implement the delete post feature using `useMutation` hook. 

```jsx
// src/Posts.jsx

import { useEffect, useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
                  // ^^^^^^^^^^ even the delete button is rendered in PostDetail component, set delete mutation in Posts component is more appropriate
import { PostDetail } from "./PostDetail";
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";
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

  // delete mutation, which will return a function `mutate`
  const deleteMutation = useMutation({
     // ^^^^^^^^^^^^^^ usually we don't destruct the `mutate` function because we still need to alias it
    mutationFn: (postId) => deletePost(postId),
              // ^^^^^^ the argument we pass to `mutate` function, will be passed to `mutationFn`
  })

  const { data, isLoading, isError, error, isFetching } = useQuery({
    queryKey: ["posts", currentPage],
    queryFn: () => fetchPosts(currentPage),
    staleTime: 2000,
  })

  if (isLoading) return <p>Loading...</p>;
  if (isError) return <p>Opps! Something went wrong: {error.toString()}</p>;

  return (
    <>
      {isFetching && <p>Updating...</p>}
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
      {selectedPost && <PostDetail post={selectedPost} deleteMutation={deleteMutation} />}
                                                   {/* ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ just prop it down to PostDetail component, where the delete button is rendered */}
    </>
  );
}
```

```jsx
// src/PostDetail.jsx 

import { useQuery } from "@tanstack/react-query";
import { fetchComments } from "./api";
import "./PostDetail.css";

export function PostDetail({ post, deleteMutation }) {
  //                               ^^^^^^^^^^^^^^

  const { data, isLoading, isError, error } = useQuery({
    queryKey: ["comments", post.id],
    queryFn: () => fetchComments(post.id),
  })

  if (isLoading) return <p>Loading comments...</p>;
  if (isError) return <p>Opps! Something went wrong: {error.toString()}</p>;

  return (
    <>
      <h3 style={{ color: "blue" }}>{post.title}</h3>
      <button
        onClick={() => {
          deleteMutation.mutate(post.id);  // call the `mutate` function with the postId to delete the specific post
        }}
      >Delete</button> <button>Update title</button>
      <p>{post.body}</p>
      <h4>Comments</h4>
      {data.map((comment) => (
        <li key={comment.id}>
          {comment.email}: {comment.body}
        </li>
      ))}
    </>
  );
}

```
