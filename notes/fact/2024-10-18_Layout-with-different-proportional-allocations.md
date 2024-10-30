---
date: 2024-10-18
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Layout with different proportional allocations

We can use `flex` or `grid` to set blocks with different proportions, here is an example from Tailwind CSS:

**Flex box**

```tsx
<div className="flex">
  <div className="flex-1">1</div>
  <div className="flex-2">2</div>
  <div className="flex-3">3</div>
</div>
```

**Grid**
```tsx
<div className="grid grid-cols-6">
  <div className="col-span-1">1</div>
  <div className="col-span-2">2</div>
  <div className="col-span-3">3</div>
</div>
```
