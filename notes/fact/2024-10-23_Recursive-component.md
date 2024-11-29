---
date: 2024-10-23
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Recursive component

Here is an example of a recursive component:

## Case: comments of a post

![recursive-comp-requirement.png](../assets/imgs/recursive-comp-requirement.png)

In this example, we have a Post that can have multiple Comments, and each Comment can also have multiple Comments, forming a recursive structure.


## Used components

![recursive-components.png](../assets/imgs/recursive-components.png)

![recursive-used-components.png](../assets/imgs/recursive-used-components.png)


**Logic to render comments recursively**

We use `CommentList` to collect comments without a parentId and use `CommentShow` to render them. However, `CommentShow` itself, in addition to rendering the comment props, will also render comments with the same comment as the parentId by using itself with these comments, achieving a recursive effect.

**Big issue: how to get the data of big list of comments**

The simplest and most classic way is to query all comments in `CommentList`, and then pass them to all `CommentShow` through props, but in Next.js we can also use Next.js features to do it in a different way.

1. [[2024-10-23_Example-of-recursive-component-and-query-option-1.5|Here]] is an example of recursive component and it queries comments in root component and props them to all children components.
