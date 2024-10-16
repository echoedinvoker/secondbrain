---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Cache control   Disable Caching

Don't do any caching at all. There are two options to disable caching:

```tsx
export const revalidate = 0;

export default async function Page() {
  // Never caches!
}
```

```tsx
export const dynamic = 'force-dynamic';

export default async function Page() {
  // Never caches!
}
```

**Only use this method when**:
1. don't know when data changes (e.g. using outside API to get data)
2. when we expect data to be different with every request
3. the user still expects to see up to date data
