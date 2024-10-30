---
date: 2024-10-18
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Implement a popup menu on Avatar

```tsx
export default async function Header() {
  return (
      <Avatar />  // assume we need to add a popup menu to this Avatar
  );
}
```

We can use the `Popover` components from `next/link` to create a popup menu on the Avatar. The `Popover` component is a wrapper component that provides the functionality to create a popup menu. It consists of three components: `Popover`, `PopoverTrigger`, `PopoverContent` and more (Check the official documentation for more details).

Here is an simple example of how to create a popup menu on the Avatar:

```tsx
export default async function Header() {
  return (
    <Popover placement="left"> {/* This sets the position of the menu */}
      <PopoverTrigger>  {/* This makes the Avatar clickable to open the menu */}
        <Avatar />
      </PopoverTrigger>
      <PopoverContent>
        {/* Add menu items here */}
      </PopoverContent>
    </Popover>
  );
}

```
