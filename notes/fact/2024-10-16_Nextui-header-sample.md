---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Nextui header sample

```tsx
// src/components/header.tsx 
import { auth } from "@/auth"
import { Input, Navbar, NavbarBrand, NavbarContent, NavbarItem } from "@nextui-org/react"
import Link from "next/link"

export default async function Header() {
  const session = await auth()

  return (
    <Navbar className="shadow mb-6">  {/* we can simply use shadow to distinguish the header and the main content */}
      <NavbarBrand>
        <Link href="/" className="font-bold">Discuss</Link>  {/* Left brand and it can be clicked to go to the home page */}
      </NavbarBrand>
      <NavbarContent justify="center">
        <NavbarItem>  {/* I don't know why we need to use NavbarItem inside NavbarContent, it seems redundant... */}
          <Input />  {/* Search bar */}
        </NavbarItem>
      </NavbarContent>
      <NavbarContent justify="end">  {/* Right side usually used to indicate the user sign in status */}
        <NavbarItem>
          {session?.user ? <div>Signed In</div> : <div>Signed Out</div>}
        </NavbarItem>
      </NavbarContent>
    </Navbar>
  )
}

```

![header-nextui.png](../assets/imgs/header-nextui.png)
