---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Calling server action in traditional way in the client component

```tsx
'use client';

import { serverAction } from '@/actions';

function ClientComponent() {
  const [data, setData] = useState(null);

  const handleClick = async () => {
    await serverAction(data);      // just call server action like a normal function
  };

  return (
    <button onClick={handleClick}>Click me</button>
  );
}

```

Usually, server actions will change the data in the database, so typically functions like `redirect` are used to redirect users to other pages. However, in the example above, assuming that serverAction has implemented `redirect` but does not wait for the database data to be updated before redirecting.

To solve the above problem, we usually use `startTransition` to wrap server actions as follows:

```tsx
'use client';

import { serverAction } from '@/actions';

function ClientComponent() {
  const [data, setData] = useState(null);

  const handleClick = () => {
    startTransition(async () => {  // startTransition will delay redirect until the data is updated
      await serverAction(data);
    });
  };

  return (
    <button onClick={handleClick}>Click me</button>
  );
}

```

**Benefits:**

1. More in line with our usual use of functions.
2. Not limited to `onSubmit` events, but can also be used in other events.

**Drawbacks:**

1. Need javascript environment support.
