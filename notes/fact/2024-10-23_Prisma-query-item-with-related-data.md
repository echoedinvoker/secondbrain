---
date: 2024-10-23
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Prisma query item with related data

```prisma
// prisma/schema.prisma

model User {
  id            String    @id @default(cuid())
  name          String?
  email         String?   @unique
  Post          Post[]
  Comment       Comment[]
}

model Topic {
  id          String @id @default(cuid())
  slug        String @unique
  description String
  posts       Post[]
}

model Post {
  id      String @id @default(cuid())
  title   String
  content String
  userId  String
  topicId String

  user     User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  topic    Topic     @relation(fields: [topicId], references: [id])
  comments Comment[]
}

model Comment {
  id       String  @id @default(cuid())
  content  String
  postId   String
  userId   String
  parentId String?

  parent   Comment?  @relation("Comments", fields: [parentId], references: [id], onDelete: Cascade)
  post     Post      @relation(fields: [postId], references: [id], onDelete: Cascade)
  user     User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  children Comment[] @relation("Comments")
}

```
```ts
// src/db/index.ts 
import { PrismaClient } from "@prisma/client";

export const db = new PrismaClient()

```

Above is the schema and client of Prisma, where we can see that Post is related to Topic, User, and Comment. Therefore, when we query a post, we can also retrieve additional related data such as topic, user, comment, etc. Here is an example:

```ts
// src/db/queries/posts.ts 
import { Post } from "@prisma/client";
import { db } from "..";

export type PostWithData = (Post & {
  topic: { slug: string };
  user: { name: string | null };
  _count: { comments: number };
})

export function fetchPostsByTopicSlug(slug: string): Promise<PostWithData[]> {
  return db.post.findMany({
    where: { topic: { slug } },
    include: {                                // `include` is used to query related data
      topic: { select: { slug: true } },
      user: { select: { name: true } },
      _count: { select: { comments: true } }
    }
  })
}

```

