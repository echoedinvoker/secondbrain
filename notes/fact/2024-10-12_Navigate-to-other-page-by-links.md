---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Navigate to other page by links

```bash
mattc@x ~/r/my-app (main)> tree src/
src/
└── app
    ├── layout.tsx
    ├── page.tsx
    └── reliability
        └── page.tsx

```

```tsx
// src/app/page.tsx
import Link from 'next/link';

export default function Home() {
  return (
    <>
      <div>Home</div>
      <Link href="/reliability">to Reliability</Link>  {/* Use Link component to navigate to other page */}
    </>
  );
}

```
