---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Pagination

There are two ways to implement pagination in React Query.

The first is for the component to track the current page using its own state, and the second is to use React Query to track the current page.

The latter is used for infinite scroll situations, so here we will introduce the first way (component state).

```jsx
// src/Posts.jsx 

import { useState } from "react";

import { fetchPosts, deletePost, updatePost } from "./api";
import { PostDetail } from "./PostDetail";
import { useQuery } from "@tanstack/react-query";
const maxPostPage = 10; // assume that we have 10 pages of posts

export function Posts() {
  const [currentPage, setCurrentPage] = useState(1); // create a state of component to track the current page
  const [selectedPost, setSelectedPost] = useState(null);

  const { data, isLoading, isError, error } = useQuery({
    queryKey: ["posts", currentPage],
    //                  ^^^^^^^^^^^ add currentPage to the query key, it should be a value in the below query function, or it just make non-sense
    queryFn: () => fetchPosts(currentPage),
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ create an anonymous function to fetch posts with the currentPage
  })

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

        {/* implement last page button */}
        <button
          disabled={ currentPage <= 1 }
          onClick={() => {
            setCurrentPage((prevPage) => prevPage - 1)
          }}>
          Previous page
        </button>

        {/* present current page */}
        <span>Page {currentPage}</span>

        {/* implements next page button */}
        <button
          disabled={ currentPage >= maxPostPage }
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

The point is that all items in the query key except the first one are dynamic values in the query function, so that it makes sense.

![pagination.png](../assets/imgs/pagination.png)

So when we click on a button to enter a page that has not been opened yet, React Query will generate a new query slot. Therefore, the status of this query slot will initially be `loading`, causing us to see `...Loading` on the screen. This may be a poorer experience for users. We will address and fix this in the next topic.
