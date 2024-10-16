---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Better layout of header

```tsx
//src/components/header.tsx
import Link from "next/link";

export default function Header() {
  return (
    <div>
      <nav className="
        relative                            // It create new flow, so we have better control of the z-index
        container flex-wrap                 // RWD purpose
        flex justify-between items-center   // flexbox (inner spacing)
        mx-auto p-8"                        // outer spacing and place thie nav container in the center horizontally
      > 
        <Link href="/" className="font-bold text-3xl">  
          Home
        </Link>
        <div className="space-x-4 text-xl"> // group links together to access justify-between layout
          <Link href="/performance">Performance</Link>
          <Link href="reliability">Reliability</Link>
          <Link href="scale">Scale</Link>
        </div>
      </nav>
    </div>
  );
}

```
