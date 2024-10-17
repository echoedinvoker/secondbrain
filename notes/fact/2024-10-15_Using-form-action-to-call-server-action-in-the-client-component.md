---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Using form action to call server action in the client component

```tsx
'use client';

import { serverAction } from '@/actions';

function ClientComponent() {
  const [data, setData] = useState({});

  const bindedServerAction = serverAction.bind(null, data);
  // through this fn, serverAction will get args (data, formData) when called

  return (
    <form action={bindedServerAction}>
      <button type="submit">Submit</button>
    </form>
  );
}
```

**Benefits:**

1. Able to use in an environment without JavaScript.
2. You can also use input to carry additional form information to the server action.

**Drawbacks:**

1. Can only be triggered by onSubmit, cannot be triggered by other means to trigger server action.
