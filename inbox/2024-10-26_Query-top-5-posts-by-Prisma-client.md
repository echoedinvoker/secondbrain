---
date: 2024-10-26
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Query top 5 posts by Prisma client

```prisma
// prisma/schema.prisma
model Topic {
  id          String @id @default(cuid())
  slug        String @unique
  description String
  posts       Post[]

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id      String @id @default(cuid())
  title   String
  content String
  userId  String
  topicId String

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user     User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  topic    Topic     @relation(fields: [topicId], references: [id])
  comments Comment[]    // post is related to many comments, so we can sort queried posts by the number of comments
}

model Comment {
  id       String  @id @default(cuid())
  content  String
  postId   String
  userId   String
  parentId String?

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  parent   Comment?  @relation("Comments", fields: [parentId], references: [id], onDelete: Cascade)
  post     Post      @relation(fields: [postId], references: [id], onDelete: Cascade)
  user     User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  children Comment[] @relation("Comments")
}

```

Assuming we want to query the posts with the highest number of 5 comments, sorted from most to least.

```ts
// src/db/queries/posts.ts 
import { Post } from "@prisma/client";
import { db } from "..";

export type PostWithData = (Post & {
  topic: { slug: string };
  user: { name: string | null };
  _count: { comments: number };
})

export function fetchTopPosts(): Promise<PostWithData[]> {
  return db.post.findMany({
    orderBy: [
      {
        comments: {
          _count: "desc"  // sort by the number of comments in descending
        }
      }
    ],
    take: 5,  // limit to 5 posts only

    // other fields to include for required data
    include: {
      topic: { select: { slug: true } },
      user: { select: { name: true } },
      _count: { select: { comments: true } }
    },
  })

}

```
