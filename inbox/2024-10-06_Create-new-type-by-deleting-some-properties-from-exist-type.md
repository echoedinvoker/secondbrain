---
date: 2024-10-06
type: fact
aliases:
  -
hubs:
  - "[[Typescript]]"
---

# Create new type by deleting some properties from exist type

```typescript
interface Person { // interface can be replaced with type or even class
  name: string;
  age: number;
  location: string;
}

type PersonWithoutLocation = Omit<Person, 'location'>;
```
