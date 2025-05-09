---
date: 2025-04-30
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Event from Child to Parent

Vue 3 provides a way to communicate from child components to parent components using the `emit` method. This is useful when you want to notify the parent component of an event that occurred in the child component.

```vue
<!--Child-->
<template>
  ...
  <button @click="$emit('register', title)">Register</button>
      <!--^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ when the button is clicked, it emits the event `register` with the value of `title`-->
      <!--                                 value is optional, you can only emit the event without any value-->
  ...
</template>

<script setup lang="ts">

defineEmits(['register'])  // 1. Define the event `register`

</script>
```

```vue
<template>
  <EventCard
    @register="console.log('Registered for', $event)"
    <!-- use @register to listen for the event `register` emitted from the child component -->
    <!-- $event is the value emitted from the child component -->
  />
</template>
