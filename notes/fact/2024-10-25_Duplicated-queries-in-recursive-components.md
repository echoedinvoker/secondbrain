---
date: 2024-10-25
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Duplicated queries in recursive components

In [[2024-10-23_Recursive-component|recursive component]], we query in `CommentList`` component and then props the result to all recursive components `CommentShow`.

We can change it to query in each `CommentShow` component and utilize [[2024-10-25_Duplicated-queries-and-cache-system|Next.js's cache system]] to avoid duplicate queries.

```ts
// src/db/queries/comments.ts
import { Comment } from "@prisma/client";
import { db } from "../index";
import { cache } from "react";

export type CommentWithAuthor = Comment & {
  user: {
    name: string | null;
    image: string | null;
  };
}

export const fetchCommentsByPostId = cache((postId: string): Promise<CommentWithAuthor[]> => { // refactor query function to arrow function
  return db.comment.findMany({                                                                 // then wrap it with cache
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
})

```

```tsx
// src/app/topics/\[slug\]/posts/\[postId\]/page.tsx
import Link from "next/link";
import PostShow from "@/components/posts/post-show";
import CommentList from "@/components/comments/comment-list";
import CommentCreateForm from "@/components/comments/comment-create-form";
import paths from "@/paths";

interface PostShowPageProps {
  params: {
    slug: string;
    postId: string;  // instead of props fetch function, props postId now
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
      <CommentList postId={postId} />  {/* props postId to CommentList, instead of fetch function */}
    </div>
  );
}

```

```tsx
// src/components/comments/comment-list.tsx
import CommentShow from "@/components/comments/comment-show";
import { fetchCommentsByPostId, type CommentWithAuthor } from "@/db/queries/comments";

interface CommentListProps {
  postId: string; // get postId from props instead of fetch function
}

export default async function CommentList({ postId }: CommentListProps) {
  const comments = await fetchCommentsByPostId(postId)  // fetch comments by postId in the component which really needs it

  const topLevelComments = comments.filter(
    (comment) => comment.parentId === null
  );
  const renderedComments = topLevelComments.map((comment) => {
    return (
      <CommentShow
        key={comment.id}
        commentId={comment.id}
        postId={postId}  // props postId instead of queried result to CommentShow
      />
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

```tsx
// src/components/comments/comment-show.tsx 
import Image from "next/image";
import CommentCreateForm from "@/components/comments/comment-create-form";
import { fetchCommentsByPostId } from "@/db/queries/comments";

interface CommentShowProps {
  commentId: string;
  postId: string;  // accept postId from props, instead of queried result
}

export default async function CommentShow({ commentId, postId }: CommentShowProps) {
  const comments = await fetchCommentsByPostId(postId);     // query comments by postId in the component which really needs it
  const comment = comments.find((c) => c.id === commentId);     // because of Next.js cache system, this query will not be duplicated

  if (!comment) {
    return null;
  }

  const children = comments.filter((c) => c.parentId === commentId);
  const renderedChildren = children.map((child) => {
    return (
      <CommentShow key={child.id} commentId={child.id} postId={postId} />  // props postId to recursive components
    );                                                                     // each CommentShow will query comments by itself and
  });                                                                      // Next.js will ensure there is no any duplicated queries automatically

  return (
    <div className="p-4 border mt-2 mb-1">
      <div className="flex gap-3">
        <Image
          src={comment.user.image || ""}
          alt="user image"
          width={40}
          height={40}
          className="w-10 h-10 rounded-full"
        />
        <div className="flex-1 space-y-3">
          <p className="text-sm font-medium text-gray-500">
            {comment.user.name}
          </p>
          <p className="text-gray-900">{comment.content}</p>

          <CommentCreateForm postId={comment.postId} parentId={comment.id} />
        </div>
      </div>
      <div className="pl-4">{renderedChildren}</div>
    </div>
  );
}

```
