---
date: 2025-04-30
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Simple Tailwind Layout

Here is an example of creating a simple layout using Tailwind CSS.

```vue
<!--src/App.vue-->

<template>
  <main class="container mx-auto my-8 space-y-8">
    <h1 class="text-4xl font-medium">Event Booking App</h1>
    <h2 class="text-2xl font-medium">All Events</h2>
    <section class="grid grid-cols-2 gap-8">
      <EventCard v-for="i in 8" :key="i" />
    </section>
    <h2 class="text-2xl font-medium">Your Bookings</h2>
  </main>
</template>
```

- Use `container` to set max-width according to screen size.
- Use `mx-auto` to center the container.
- Use `my-8` to set vertical margin outside the container.
- Use `space-y-8` to set vertical spacing between child elements.
    - *section* is a child element of *main*.
    - Use `grid` to create a grid layout on the *section* element.
    - Use `grid-cols-2` to set two columns in the grid.
    - Use `gap-8` to set spacing between grid items. (Vertical and horizontal)


