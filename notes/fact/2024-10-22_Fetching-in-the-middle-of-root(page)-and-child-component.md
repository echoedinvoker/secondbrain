---
date: 2024-10-22
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Fetching in the middle of root(page) and child component

This option is better than [[2024-10-22_Fetching-in-root(page)-or-child-component?|fetching in root(page) or child component]], but it takes three steps to implement:


1. Write specific queries in a file

```ts
// src/queries/posts.ts
export type PostWithData = (Post & {
  topic: { slug: string },
  _count: { comments: number },
  user: { name: string }
})

export function fetchPostsBySlug(slug: string): Promise<PostWithData[]> {
  //...
}

export function fetchTopPosts(): Promise<PostWithData[]> {
  //...
}

```
2. Write interface for the child component

```tsx
// src/components/post-list.tsx
interface PostListProps {
  fetchPosts: () => Promise<PostWithData[]>   // instead of data from the parent component,
}                                            // it accepts a specific query function from the parent component

export default function PostList({ fetchPosts }: PostListProps) {
  //...
}

```

3. Pick the right query in the root(page) component

```tsx
// src/app/page.tsx
import { fetchTopPosts } from '../queries/posts'  // parent component decides which query to use, but not query itself
import PostList from '../components/post-list'

export default function HomePage() {
  // ...
  return {
    <PostList fetchPosts={fetchTopPosts} />
  }
}

```
```tsx
// src/app/topic/[slug]/page.tsx
import { fetchPostsBySlug } from '@/queries/posts'
import PostList from '@/components/post-list'

export default function TopicShowPage({ slug }) {
  // ...
  return {
    <PostList fetchPosts={() => fetchPostsBySlug(slug)} />
  }
}

```

This option centralize query decisions in the root(page) component, but let child components do the fetching. So it has the benefits of both [[2024-10-22_Fetching-in-root(page)-or-child-component?|fetching in root(page) or child component]] options.
