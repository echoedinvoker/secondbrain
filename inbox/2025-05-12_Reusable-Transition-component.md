---
date: 2025-05-12
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Reusable Transition component

In [[2025-05-12_Component-Transition|Component Transition]], we used a `Transition` component to animate the components when switching between tabs. However, we can create a reusable component to handle the transition for any component.

```vue
<!-- src/components/FadeTransition.vue -->
<template>
  <Transition name="fade" mode="out-in">
    <slot />
  </Transition>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

Above, we collect attribute and styles related to transition in a single component `FadeTransition`, so it is easier to reuse and maintain.

Then we can use the `FadeTransition` component in any component which needs transition effect:

```vue

<script setup lang="ts">
...
import FadeTransition from '@/components/FadeTransition.vue'; // simple import
...
</script>

<template>
  ...
    <FadeTransition> <!-- wrap the component which needs transition effect -->
      <component :is="currentTabComponent" />
    </FadeTransition>
  ...
</template>
```

It also make the code cleaner and easier to read in the component where we use it.


