---
date: 2024-10-17
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Split template codes from return

In order to avoid overly long return codes, we can extract template codes, especially for parts that require logical judgment. This can make the return part more concise and easier to read.


**Before**
```tsx
export default function component() {

  return (
    <div>
      {condition ? (
      <div>content</div>
      ) : (
      <div>other content</div>
      )}
    </div>
  )
}
```

**After: long syntax**
```tsx
export default function component() {

  let content: React.ReactNode;  // extract template codes, longer but easier understand codes
  if (condition) {
    content = <div>content</div>;
  } else {
    content = <div>other content</div>;
  }

  return <div>{content}</div>;
}
```

The above writing is easier to understand, we can also use short syntax to write.

**After: short syntax**
```tsx
export default function component() {

  const content = condition ? <div>content</div> : <div>other content</div>;  // short syntax

  return <div>{content}</div>;
}
```
```
