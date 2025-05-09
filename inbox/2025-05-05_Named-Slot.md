---
date: 2025-05-05
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Named Slot

```vue
<!--EventCard.vue-->
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
<!--RoundedCard.vue-->
<template>
  <div class="border border-gray-200 bg-white rounded-md">
    <slot></slot>
  </div>
</template>
```

The component EventCard above uses RoundedCard to create the outer frame and background color of the card. RoundedCard uses a slot so that EventCard can place any content in this slot, providing great flexibility.

However, in the markup of EventCard, we can see the use of border-b to separate the slot's content into header, content, and footer. If this kind of separation is frequently needed, we can create another wrapper component using named slots to create these separations and related styles, so that users do not need to repeat these styles and can focus on the content.

Let's create a new component called SectionedCard.vue to achieve this.

```vue
<!--SectionedCard.vue-->
<template>
  <RoundedCard>
    <h3 class="p-4 font-medium text-xl border-b border-gray-200">
      <slot name="header"></slot>
    </h3>
    <div class="p-4 border-b border-gray-200">
      <slot></slot>
    </div>
    <div class="p-4">
      <slot name="footer"></slot>
    </div>
  </RoundedCard>
</template>

<script setup lang="ts">
import RoundedCard from '@/components/RoundedCard.vue';
</script>
```

Now we can use SectionedCard in EventCard to create the same card with out-of-the-box styles and separations.

```vue
<template>
  <SectionedCard>
    <template #header>{{ title }}</template> <!-- # is shorthand for v-slot, it means `{{ title }}` will be passed to the slot named `header` -->
    {{ when }} <!-- content not wrapped by `template` will be passed to the default slot -->
    <template #footer> <!-- we can still pass complex content to the named slot -->
      <p>{{ description }}</p>
      <div class="flex justify-end">
        <Button label="Register" @click="$emit('register', title)" />
      </div>
    </template>
  </SectionedCard>
</template>

<script setup lang="ts">
import Button from '@/components/Button.vue'
import SectionedCard from '@/components/SectionedCard.vue'

defineProps({
  title: String,
  when: String,
  description: String,
})
defineEmits(['register'])
</script>
```

Compare the two implementations of EventCard. The last one is much cleaner and easier to read because it's more focused on the content rather than the layout and styling.

> Note that users can also pass complex content to named slots, so in SectionCard.vue, we wrap the slot's markup with a `div` instead of the original `p`.



