---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Benefits and limitations of server components

Because server components are executed on the server, they have several benefits and limitations compared to client-side components.

**Benefits of server components:**

1. Server components can access server-side resources like [[2024-10-13_Directly-query-db-and-render-the-result|database]] or APIs.
2. Server components can be asynchronous, so they can use asynchronous functions like `fetch` or `readFile` directly.

```tsx
export default async function Home() { // Server component can be async
  const res = await fetch('https://api.example.com/data'); // So it can use async functions like fetch
  const data = await res.json(); // Server components work on the server, so they can access server-side resources

  return <div>{data}</div>;

```

**Limitations of server components:**

1. Server components cannot use hooks like `useState` or `useEffect`.
2. Server components cannot use event handlers like `onClick` or `onChange`.


