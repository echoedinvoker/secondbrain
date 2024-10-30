---
date: 2024-10-23
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Get type from function

```ts
// src/db/queries/posts.ts
import { Post } from "@prisma/client";
import { db } from "..";

export type PostWithData = (Post & {  // Here we manually define the type of function fetchPostsByTopicSlug's return value
  topic: { slug: string };            // This way need well knowledge of TypeScript
  user: { name: string | null };      // And whenever the return type of fetchPostsByTopicSlug changes
  _count: { comments: number };       // We need to update this type manually also
})

export function fetchPostsByTopicSlug(slug: string): Promise<PostWithData[]> {
  return db.post.findMany({
    where: { topic: { slug } },
    include: {
      topic: { select: { slug: true } },
      user: { select: { name: true } },
      _count: { select: { comments: true } }
    }
  })
}

```

We actually don't need to manually define the type `PostWithData`, we can directly get its return type from the function `fetchPostsByTopicSlug`.

```ts
import { db } from "..";

export type PostWithData = Awaited<   // because fetchPostsByTopicSlug is async function, we need to use Awaited to decompose the Promise
  ReturnType<                         // ReturnType gets the return type of a function
    typeof fetchPostsByTopicSlug      // Use typeof to get the type of function fetchPostsByTopicSlug
  >
>[number];                            // Because fetchPostsByTopicSlug returns an array, we use [number] to get the type of the array elements

export function fetchPostsByTopicSlug(slug: string) {   // Because the type definition is from this function, we can not use Promise<PostWithData[]> here
  return db.post.findMany({
    where: { topic: { slug } },
    include: {
      topic: { select: { slug: true } },
      user: { select: { name: true } },
      _count: { select: { comments: true } }
    }
  })
}

```

The above method allows us to not manually maintain the type of `PostWithData`. Just modify the return type of `fetchPostsByTopicSlug`, and `PostWithData` will be automatically updated.

>However, because the type is obtained from the function, it is not possible to verify the return type of the function itself. Therefore, this method can only be used when it is already confirmed that the return type of the function is correct.

(If time allows, it is still recommended to use the more rigorous first method)
