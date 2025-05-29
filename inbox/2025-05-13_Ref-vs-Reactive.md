---
date: 2025-05-13
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Ref vs Reactive

## Ref

- Accept any type of value
- Deeply reactive

## Reactive

- Only accepts objects
- Deeply reactive

### Can't assign whole object

```js
let state = reactive({ count: 0 })
state = reactive({ count: 1 }) // no any reactive because this is a new object
```

Therefore, using `reactive` actually transforms the original object to make it reactive, so if the object is completely replaced, it will not have any reactive.


### Can't destructure

```js
let state = reactive({ count: 0 })
let { count } = state
count++ // destructure value `count` don't have reactivity
state.count++ // this is the right way
```

## Conclusion

So under normal circumstances, we should use `ref` instead of `reactive`.
