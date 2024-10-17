---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Cache control - Time Based


```tsx
export const revalidate = 3; // re-render cached page every 3 seconds in the server

export default async function Page() {
  const snippets = await db.snippets.findMany();

  return <div>{snippets.map(...)}</div>;

}

```

**Best senario**:

The forum like Reddit that is very suitable for using this method, because even though there are constantly new posts, users don't actually care about seeing new posts immediately, so this method can reduce the burden on servers A LOT.

You can even set the revalidate to several minutes. And it's still fine.
