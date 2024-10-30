---
date: 2024-10-26
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Using Nextui Skeleton component to create better loading spinner

For [[2024-10-26_Streaming-contents-for-server-components|Suspense]] fallback, we can use the Skeleton component from Nextui to create a better loading spinner.

```tsx
export default function Page() {
  return (
    <div>
      <h1>My page</h1>
      <Suspense fallback={<MyComponentLoading />}>  {/* Instead of <div>Loading...</div>, we create a loading component */}
        <MyComponent />
      </Suspense>
    </div>
  );
}
```

```tsx
export default function MyComponent() {
  return (
    <div className="m-4">
      <div className="my-2">
        <h2 className="text-lg font-bold">Title</h2>
      </div>
      <div className="p-4 border rounded space-y-2">
        <p className="text-sm">Description</p>
        <p className="text-sm">Description</p>
        <p className="text-sm">Description</p>
      </div>
    </div>
  );
}
```

```tsx
export default function MyComponentLoading() {
  return (
    <div className="m-4"> {/* Just copy paste the MyComponent layout */}
      <div className="my-2">
        <Skeleton className="h-8 w-24" />  {/* Skeleton will create a loading bar */}
      </div>
      <div className="p-4 border rounded space-y-2">
        <Skeleton className="h-6 w-32" />  {/* We don't actually know the size of the text */}
        <Skeleton className="h-6 w-32" />  {/* So we just use Skeleton to create some loading bar */}
        <Skeleton className="h-6 w-32" />  {/* These bars don't need to be the same size, just similar */}
      </div>
    </div>
  );
}
```

