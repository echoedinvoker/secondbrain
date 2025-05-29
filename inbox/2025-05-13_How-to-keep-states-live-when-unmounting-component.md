---
date: 2025-05-13
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# How to keep states live when unmounting component

```vue
<template>
  <component :is="component" />
  <button @click="component = 'A'">A</button>
  <button @click="component = 'B'">B</button>
</template>

<script setup>
import { ref } from 'vue'
import A from './A.vue'
import B from './B.vue'

const component = ref(A)
</script>
```

```vue
<template>
  <div>
    <h1>A</h1>
    <p>Count: {{ count }}</p>
    <button @click="count++">Increment</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>
```

When you switch between components A and B, the state of component A and B will be reset. To keep the state of component A and B when switching, you can use `<KeepAlive>`.

```vue
<template>
  <keep-alive> <!-- wrap the component with <keep-alive> -->
    <component :is="component" />
  </keep-alive>
  <button @click="component = 'A'">A</button>
  <button @click="component = 'B'">B</button>
</template>
```

Now, when you switch between components A and B, the state of component A and B will be preserved. The `<keep-alive>` component caches the state of the components that are unmounted, so when you switch back to them, their state is restored.

Other options to set states in the composable functions:

```javascript
// useCounter.js
import { ref } from 'vue'

const count = ref(0) // global state

export function useCounter() {
  function increment() {
    count.value++
  }

  return {
    count, // return the global state inside the composable
    increment
  }
}
```

```vue
<!--A.vue-->
<template>
  <div>
    <h1>A</h1>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script setup>
import { useCounter } from './useCounter.js'

const { count, increment } = useCounter()
</script>
```

Now, when you switch between components A and B, the state of useCounter will be preserved as well.
