---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Benefits and limitations of client components

Client components are executed on the client side, so they cannot perform server-side operations. But they can use hooks and event handlers as regular components we use in React.

You need to tell Next.js to render a component on the client side by adding `'use client';` at the top of the component file. Because by default, all components are rendered on the server side.


**Benefits of client components:**

1. Client components can use hooks.
2. Client components can use event handlers.

```tsx
'use client'; // This tells Next.js to render this component on the client side

export default function Home() {
  const [count, setCount] = useState(0); // Client component can use hooks

  const handleClick = () => {
    setCount(prevCount => prevCount + 1);
  };
  return <button onClick={handleClick}>{count}</button>; // Client component can use event handlers
}
```

**Limitations of client components:**

1. Obviously, client components cannot perform server-side operations. such as fetching data from an API or querying a database.

2. Client components cannot use use server components. but server components can use client components.


**Client components will be rendered on server-side when first requested**

So you might think that client components are always rendered on the client side, but that's not true. When first requested, all components are rendered on the server side. After the initial request, the client components are rendered on the client side.

