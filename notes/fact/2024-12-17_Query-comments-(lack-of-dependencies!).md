---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Query comments (lack of dependencies!)

## Description of Requirement

```jsx
// src/Posts.jsx

// ...

export function Posts() {
  const [currentPage, setCurrentPage] = useState(0);
  const [selectedPost, setSelectedPost] = useState(null);  // 2. selected post is stored into this state, state triggers re-render

  // ...

  return (
    <>
      <ul>
        {data.map((post) => (
          <li
            key={post.id}
            className="post-title"
            onClick={() => setSelectedPost(post)} // 1. when user clicks on a post title
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

      {/* state of selectedPost props into PostDetail component and re-render it */}
      {selectedPost && <PostDetail post={selectedPost} />}  

    </>
  );
}
```

```jsx
// src/PostDetail.jsx 

import { fetchComments } from "./api";  // this is fetch function, which need a parameter postId
import "./PostDetail.css";

export function PostDetail({ post }) {

  // replace with useQuery
  const data = [];

  return (
    <>
      <h3 style={{ color: "blue" }}>{post.title}</h3>
      <button>Delete</button> <button>Update title</button>
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

## Solution

We can use the way similar to how we [[2024-12-17_Creating-Queries-with-useQuery|queried posts before]]:

```jsx
// src/PostDetail.jsx 

import { fetchComments } from "./api";
import "./PostDetail.css";

export function PostDetail({ post }) {

  // use useQuery to query comments
  const { data, isLoading, isError, error } = useQuery({
    queryKey: ["comments"],  // if you use React Query ESlint plugin, here will show error for lack of dependencies
    queryFn: () => fetchComments(post.id),
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ because fetch function need a parameter postId, so we need to add it as a anonymous function
    //                                    and it is the reason why we got ESlint error above (we will talk about it later)
  })

  // handle loading and error states with properties from useQuery
  if (isLoading) return <p>Loading comments...</p>;
  if (isError) return <p>Opps! Something went wrong: {error.toString()}</p>;

  return (
    <>
      <h3 style={{ color: "blue" }}>{post.title}</h3>
      <button>Delete</button> <button>Update title</button>
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

## Test

```bash
npm run dev
# When the first click on a post title, it should render the post detail with comments on the bottom of page
# but if you continue to click on another post title, it won't refresh the comments or post details (we will fix it later)

```
```
