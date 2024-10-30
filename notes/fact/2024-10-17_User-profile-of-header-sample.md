---
date: 2024-10-17
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# User profile of header sample

```tsx
<Navbar>
  ...
  <NavbarContent justify="end">
    {session?.user ? (
      <Avatar src={session.user.image || ''} />
    ) : (
      <NavbarItem>
        <Button type="submit" color="secondary" variant="bordered">Sign In</Button>
      </NavbarItem>
      <NavbarItem>
        <Button type="submit" color="primary" variant="flat">Sign Up</Button>
      </NavbarItem>
    )}
  </NavbarContent>
</Navbar>

```

