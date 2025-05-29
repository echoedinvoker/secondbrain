---
date: 2025-05-12
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Define emits with Typescript

Before we define emits as below:

```js
const emit = defineEmits(['change', 'input'])
```

With Typescript, we can define emits with types:

```ts
const emit = defineEmits<{
  (e: 'change', id: number): void
//    ^^^^^^^^ event name
  (e: 'input', value: string): void
//             ^^^^^^^^^^^^^ parameter
}>()
```
`id` and `value` are parameters carried by the event.

In Vue 3.3+, more succinct syntax is provided as follows:

```ts
const emit = defineEmits<{
  change: [id: number]
//^^^^^^ event name
  update: [value: string]
//        ^^^^^^^^^^^^^^^ parameters, if no parameter, use `[]`
}>()
```


