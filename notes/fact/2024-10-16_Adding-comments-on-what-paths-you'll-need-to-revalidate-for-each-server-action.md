---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Adding comments on what paths you'll need to revalidate for each server action

This is final step of [[2024-10-16_Recommended-Initial-Design-before-coding-project|Recommended Initial Design]].

List all server actions and paths, and think about what paths you'll need to revalidate for each server action. 

![action-to-path.png](../assets/imgs/action-to-path.png)

Then, add comments to each server action to indicate what paths you'll need to revalidate in the future. 

```ts
export async function createTopic() {
  // TODO: revalidate home page after creating a new topic
}

export async function createPost() {
  // TODO: revalidate topic show page after creating a new post
}

export async function createComment() {
  // TODO: revalidate post show page after creating a new comment
}
```
