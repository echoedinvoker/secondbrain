---
date: 2024-10-22
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Create a item which related to other entities

When we create some item related to other different entities, we usually need to create the item with its fields and relative entities' ids, which requires us to find a way to obtain entites' ids. Here is an example:

```bash
src/
├── actions
│   └── create-post.ts
└── app
    ├── page.tsx
    └── topics
        └── [slug]
            ├── page.tsx
            └── posts
                ├── page.tsx  # assume this is the page to create a post, and it should be related to the topic and the user
                └── [postId]
                    └── page.tsx

```

```prisma
model Post {
  id      String @id @default(cuid())
  title   String
  content String
  userId  String    // the user id, we can get it from the session
  topicId String    // the topic id, but we only have the slug from wildcard folder, we need to query the topic by it

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user     User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  topic    Topic     @relation(fields: [topicId], references: [id])
  comments Comment[]
}
```

So we need to pass the slug to the `create-post` action, and query the topic by the slug to get the `topicId`. [[2024-10-22_Pass-additional-data-to-server-action-of-useFormState|how to pass additional data to server action]]
Below is an example of the server action to create a post which related to the topic and the user:

```ts
'use server'

export async function createPost(slug: string, formState: CreatePostFormState, formData: FormData): Promise<CreatePostFormState> {
  const topic = await db.topic.findFirst({
    where: { slug },
  })

  // query result has chance to be null, so we need to check it
  if (!topic) {
    return { errors: { _form: ["Topic not found"] } }  // if the topic not found, it is kind of generic error, so we use _form of form state to the frontend
  }

  // the user id is from the session, but we should check if the user is logged in also
  const session = await auth();  
  if (!session || !session.user) {
    return { errors: { _form: ["You must be logged in to create a post"] } } // if the user is not logged in, it is also kind of generic error, too
  }

  const post: Post;
  try {
    post = db.post.create({
      data: {
      title: formState.title,
      content: formState.content,
      userId: session.user.id,       // the user id from the session
      topicId: topic.id,             // the topic id from the query result
      }
    })
    return { post };
  } catch (error) {
    return { errors: { _form: ["Failed to create post"] } }  // if the post creation failed, it is also kind of generic error, too
  }

  revalidatepath( ... ) // we can revalidate the topic page to get the latest posts
  return { errors: {} };  // or we can replace return with redirect to the post page
}
```
