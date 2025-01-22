---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Query key as dependencies

In [[2024-12-17_Query-comments-(lack-of-dependencies!)|this topic]], we left a issue not resolved: the post details and the comments don't refresh when user clicks on other post. We want to solve it this topic.

## Analysis the problem

There are two conditions for re-fetching the data:
1. Any trigger event occur, there are some trigger events:
  - component remount
  - window refocus
  - running refetch function from useQuery
  - automated refetching in a interval
  - query invalidation after a mutation
2. Data is stale.

Our situation is that the second condition is met (data is outdated), but no trigger event has occurred, so no re-fetch is triggered.


## How to solve it?

We can to satisfy the first condition by invalidating the query programmatically. But it isn't a good idea. Why?

We have no reason to remove old post details and comments from the cache. Maybe user will return to the old post again.

So, we better treat each query to different post details as a different query. We can use the second item of the query key to differentiate the queries as below:

```jsx
// src/PostDetail.jsx

import { useQuery } from "@tanstack/react-query";
import { fetchComments } from "./api";
import "./PostDetail.css";

export function PostDetail({ post }) {

  const { data, isLoading, isError, error } = useQuery({
    queryKey: ["comments", post.id],
                        // ^^^^^^^ add the post id to the query key

    queryFn: () => fetchComments(post.id),
                              // ^^^^^^^ in fact, any values in the query function
                              //         should be added to query key array just like dependencies of effect
  })

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

![keys-as-dependencies.png](../assets/imgs/keys-as-dependencies.png)
