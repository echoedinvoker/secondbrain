---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Using special file to handle the error

error.tsx is a [[2024-10-13_Special-file-name-under-the-app-directory|special file name under the app directory]].

Unlike other special files, error.tsx must be client components, and it got two props by default.

```tsx
'use client';  // error.tsx must be client components

interface ErrorProps {
  error: Error;
  reset: () => void;  // reset to the initial state when the error not handled
}

export default function Error({ error, reset }: ErrorProps) {
  return (
    <div>
      <h1>Error</h1>
      <p>{error.message}</p>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

```

**Benefits:**

1. Easy to use, compare to another option.
2. The error component is reusable, and you can customize it as you like.

**Drawbacks:**

1. Page states will be wiped out when user click the reset button.
2. error.tsx is a completely different page and may not be suitable for certain requirements.
