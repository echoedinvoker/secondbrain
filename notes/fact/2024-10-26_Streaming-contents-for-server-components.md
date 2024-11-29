---
date: 2024-10-26
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Streaming contents for server components

```tsx
interface TopicPageProps {
  topicId: string;
}
export default function TopicPage({ topicId }: TopicPageProps) {
  return (
    <div>
      <h1>Topic</h1>
      <TopicShow topicId={topicId} />  {/* Server component which needs to query data */}
      <PostList topicId={topicId} />   {/* Server component which needs to query data */}
    </div>
  );
}

```


In the example above, the entire page, including the server components used, must be fully rendered on the backend before being returned to the frontend.

We can use `Suspense` to wrap the server component, allowing it to be replaced with a placeholder first, and then replacing the placeholder on the front-end page with the content of the server component after it retrieves the data.

```tsx
interface TopicPageProps {
  topicId: string;
}
export default function TopicPage({ topicId }: TopicPageProps) {
  return (
    <div>
      <h1>Topic</h1>
      <Suspense fallback={<div>Loading...</div>}>  {/* When the server component is querying data, show a loading spinner */}
        <TopicShow topicId={topicId} />   {/* until the data is ready, then replace the spinner with the server component */}
      </Suspense>
      <Suspense fallback={<div>Loading...</div>}>
        <PostList topicId={topicId} />
      </Suspense>
    </div>
  );
}

```

Usually this practice will have a better user experience.

Nextui provides `Selecton` component to help us create [[2024-10-26_Using-Nextui-Skeleton-component-to-create-better-loading-spinner|better looking loading spinner]].
