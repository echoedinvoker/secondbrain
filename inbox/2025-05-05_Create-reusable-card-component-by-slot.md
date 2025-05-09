---
date: 2025-05-05
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Create reusable card component by slot

We can use slots to create a reusable card component.

```vue
<!--RoundedCard.vue-->
<template>
  <div class="border border-gray-200 bg-white rounded-md"> <!-- define the card style -->
    <slot></slot> <!-- slot for content, it's very flexible -->
  </div>
</template>
```

Then, we can further use this component in other components.

```vue
<!-- EventCard.vue -->
<template>
  <RoundedCard>
    <h3 class="p-4 font-medium text-xl border-b border-gray-200">{{ title }}</h3>
    <p class="p-4 border-b border-gray-200">{{ when }}</p>
    <p class="p-4">{{ description }}</p>
    <section class="flex justify-end p-4">
      <Button label="Register" @click="$emit('register', title)" />
    </section>
  </RoundedCard>
</template>

<script setup lang="ts">
import Button from '@/components/Button.vue';
import RoundedCard from '@/components/RoundedCard.vue';

defineProps({
  title: String,
  when: String,
  description: String,
})
defineEmits(['register'])
</script>
```

```vue
<!--BookingItem.vue-->
<template>
  <RoundedCard>
    <div class="p-4">
      Booking to Vue Conference...
    </div>
  </RoundedCard>
</template>

<script setup lang="ts">
import RoundedCard from '@/components/RoundedCard.vue';
</script>
```

You can see that both components above use `RoundedCard` to define the outer border style and background color of the card, but the content inside the card is completely different. This is the power of slots.

This method helps us to omit a lot of code and make the style of the entire website consistent.
