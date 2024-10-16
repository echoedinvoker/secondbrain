---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Extend page background picture to the header

```tsx
//src/components/header.tsx
import Link from "next/link";

export default function Header() {
  return (
    <div className="w-full absolute text-white z-10"> {/* these classes are to make the header full width */}
      <Link href="/">Home</Link>
      <Link href="/performance">Performance</Link>
      <Link href="reliability">Reliability</Link>
      <Link href="scale">Scale</Link>
    </div>
  );
}

```
```
