---
date: 2024-10-23
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Example of recursive component and query option 1.5

1. define the specific query of comments

```ts
// src/db/queries/comments.ts 
import { Comment } from "@prisma/client";
import { db } from "../index";

export type CommentWithAuthor = Comment & {
  user: {
    name: string | null;
    image: string | null;
  };
}

export function fetchCommentsByPostId(postId: string): Promise<CommentWithAuthor[]> {
  return db.comment.findMany({
    where: {
      postId,
    },
    include: {
      user: {
        select: {
          name: true,
          image: true,
        },
      },
    },
  });
}

```

2. define the interface of the component which will do the query

```tsx
// src/components/comments/comment-list.tsx 
import CommentShow from "@/components/comments/comment-show";
import type { CommentWithAuthor } from "@/db/queries/comments";

interface CommentListProps {
  fetchComments: () => Promise<CommentWithAuthor[]>;
}

export default async function CommentList({ fetchComments }: CommentListProps) {
  const comments = await fetchComments();

  const topLevelComments = comments.filter(
    (comment) => comment.parentId === null
  );
  const renderedComments = topLevelComments.map((comment) => {
    return (
      <CommentShow                // This is the component which will render a single comment
        key={comment.id}          // And it will also use itself to render the children comments recursively
        commentId={comment.id}    // This prop is used to decide which comment to render
        comments={comments}       // Each CommentShow component will have access to all the comments,
      />                          // which queried on CommentList component and prop it to each CommentShow component (this is classic way)
    );
  });

  return (
    <div className="space-y-3">
      <h1 className="text-lg font-bold">All {comments.length} comments</h1>
      {renderedComments}
    </div>
  );
}

```


3. decide the specific query of a comment on page component and props it to the above component

```tsx
// src/app/topics/\[slug\]/posts/\[postId\]/page.tsx 
import Link from "next/link";
import PostShow from "@/components/posts/post-show";
import CommentList from "@/components/comments/comment-list";
import CommentCreateForm from "@/components/comments/comment-create-form";
import paths from "@/paths";
import { fetchCommentsByPostId } from "@/db/queries/comments";

interface PostShowPageProps {
  params: {
    slug: string;
    postId: string;
  };
}

export default async function PostShowPage({ params }: PostShowPageProps) {
  const { slug, postId } = params;

  return (
    <div className="space-y-3">
      <Link className="underline decoration-solid" href={paths.topicShow(slug)}>
        {"< "}Back to {slug}
      </Link>
      <PostShow postId={postId} />
      <CommentCreateForm postId={postId} startOpen />
      <CommentList fetchComments={() => fetchCommentsByPostId(postId)} />
    </div>
  );
}

```


