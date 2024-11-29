---
date: 2024-10-26
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Get query string in client component

In client component, we can get query string by using hook `useSearchParams`.

```tsx
import { useSearchParams } from 'next/navigation';

function MyComponent() {
  const searchParams = useSearchParams();

  return <div>Query: {searchParams.get('term') || ''}</div>; // e.g. /?term=hello
}

```

Not like [[2024-10-26_Get-query-string-in-server-component|getting query string in server component]], getting query string in client component won't make route dynamic and don't need to be page component.

**IMPORTANT**: client components which use `useSearchParams` should be wrapped in `Suspense` component. Otherwise, it will throw an error in production mode.

```tsx
function Page() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <MyComponent />  {/* MyComponent uses useSearchParams insides, so it should be wrapped in Suspense */}
    </Suspense>
  );
}
```
