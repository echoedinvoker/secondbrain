---
date: 2024-12-18
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Other useMutation Properties

In [[2024-12-18_Delete-post-with-useMutation|this topic]], we implemented a delete post feature with the `useMutation` hook but it don't has any different from using delete function directly.

So in this topic, we will learn about other properties of `useMutation` hook that can be useful, even the number of properties is much less than `useQuery` because there is no cache involved.

```jsx
// src/PostDetail.jsx

import { useQuery } from "@tanstack/react-query";
import { fetchComments } from "./api";
import "./PostDetail.css";

export function PostDetail({ post, deleteMutation }) {

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
          deleteMutation.mutate(post.id);
        }}
      >Delete</button> <button>Update title</button>

      {/* we can use other properties of deleteMutation to render different messages conditionally */}
      {deleteMutation.isPending && <p className="loading">deleting...</p>}
      {deleteMutation.isError && (
        <p className="error">
          Something went wrong when deleting: {deleteMutation.error.toString()}
        </p>
      )}
      {/* usually we don't need to show success message because changed data itself is the success message... */}
      {/* here we just show it for demonstration purpose and jsonplaceholder API doesn't really delete the post */}
      {deleteMutation.isSuccess && (
        <p className="success">
          Post has been deleted successfully!
        </p>
      )}

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

![deleting.png](../assets/imgs/deleting.png)

![success-del.png](../assets/imgs/success-del.png)


But there is a issue that even we change other post, the message still shows. That's not what we want. We need to reset the deleteMutation state when we change the post. And that's the true reason we set the deleteMutation in Posts component instead of PostDetail component.

```jsx
// src/Posts.jsx 

import { useEffect, useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
import { PostDetail } from "./PostDetail";
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";
const maxPostPage = 10;

export function Posts() {
  const [currentPage, setCurrentPage] = useState(1);
urrent page
  const [selectedPost, setSelectedPost] = useState(null);

  const queryClient = useQueryClient()

  useEffect(() => {
    if (currentPage >= maxPostPage) return;
    queryClient.prefetchQuery({
      queryKey: ["posts", currentPage + 1],
      queryFn: () => fetchPosts(currentPage + 1),
    })
  }, [currentPage, queryClient])

  // we created deleteMutation here instead of in PostDetail component before
  const deleteMutation = useMutation({
    mutationFn: (postId) => deletePost(postId),
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
            onClick={() => {
              deleteMutation.reset()  // reset the deleteMutation state in Posts component when we select a post
              setSelectedPost(post)
            }}
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
    </>
  );
}
```
