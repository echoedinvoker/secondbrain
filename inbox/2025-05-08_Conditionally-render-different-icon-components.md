---
date: 2025-05-08
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Conditionally render different icon components

```vue
<template>
  <SectionedCard>
  <div class="flex justify-between">
    <div class="flex-1">
      <h3 class="text-lg font-medium">{{ title }}</h3>
      <p class="text-sm text-gray-500">User ID: {{ user }}</p>
      <p class="text-sm text-gray-500">Status: {{ status }}</p> <!-- instead of text, we want to show an icon depending on the status -->
    </div>
    <Button label="Cancel" variant="danger"
      @click="$emit('cancelBooking')" />
  </div>
  </SectionedCard>
</template>

<script setup lang="ts">
import SectionedCard from '@/components/SectionedCard.vue';
import Button from './Button.vue';

defineEmits(['cancelBooking'])

defineProps({
  title: {
    type: String,
    required: true,
  },
  user: {
    type: Number,
    required: true,
  },
  status: {
    type: String,
    required: true,
  },
})
</script>
```

We can compute the pending state and then compute the icon based on that state from the status prop.

```js
import { LoaderCircle, Check } from 'lucide-vue-next'; // import the lucide icon components you want to use
import { computed } from 'vue';


const props = defineProps({
  ...
  status: { // this is the prop we will use to determine the pending state, then we will compute the icon
    type: String,
    required: true,
  },
})

// compute the pending state based on the status prop
const pending = computed(() => props.status === 'pending')
// compute the icon based on the pending state
const icon = computed(() => pending.value ? LoaderCircle : Check)
```

Then, we can use `component` to render the icon component dynamically in the template.

```vue
<template>
  <SectionedCard>
      <div class="flex justify-between">
        <div class="flex-1">
          <h3 class="text-lg font-medium">{{ title }}</h3>
          <p class="text-sm text-gray-500">User ID: {{ user }}</p>

          <!-- instead of text, we want to show an icon depending on the status -->
          <component :is="icon"
            :class="{ 'animate-spin': pending }" <!-- if icon is LoaderCircle, apply the spin animation by Tailwind CSS utility class `animate-spin` -->
          />

        </div>
        <Button label="Cancel" variant="danger"
          @click="$emit('cancelBooking')" />
      </div>
  </SectionedCard>
</template>
```
