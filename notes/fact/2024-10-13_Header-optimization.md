---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Header optimization


**Current header and issues**

```tsx
// src/components/header.tsx 
import Link from "next/link";

export default function Header() {
  return (
    <div>
      <Link href="/">Home</Link>
      <Link href="/performance">Performance</Link>
      <Link href="reliability">Reliability</Link>
      <Link href="scale">Scale</Link>
    </div>
  );
}

```
![issue-header.png](../assets/imgs/issue-header.png)

Obviously, the header has some issues:
1. The page background is not extending to the whole header. [[2024-10-13_Extend-page-background-picture-to-the-header|Extend page background picture to the header]]
2. The layout of the header contents is not good. [[2024-10-13_Better-layout-of-header|Better layout of header]]
