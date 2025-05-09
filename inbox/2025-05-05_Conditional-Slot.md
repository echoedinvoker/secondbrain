---
date: 2025-05-05
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Conditional Slot

In the [[2025-05-05_Named-Slot|Named Slot]], we use named slots to divide the content of the `StationedCard` component into header slot, default slot, and footer slot. Each slot has default border and padding styles, but if we also want to use `StationedCard` with only default content, in this scenario, the styles and borders of the header and footer slots will be redundant. In this case, we can use conditional slot logic to avoid rendering these slots that have not received any content.

```vue
<!-- BookingItem.vue -->
<template>
  <SectionedCard>
    Booking to Vue Conference... <!-- only pass content to default slot -->
  </SectionedCard>
</template>

<script setup lang="ts">
import SectionedCard from '@/components/SectionedCard.vue';
</script>
```

In above code, we only pass content to the default slot of `SectionedCard`, and the header and footer slots are not passed any content but are still rendered with default styles (padding and border). To avoid this, we need to modify the `SectionedCard` component to conditionally render all slots based on whether they have received any content as below:

```vue
<!-- SectionedCard.vue -->
<template>
  <RoundedCard>
    <h3
      v-if="$slots.header" <!-- only render if header slot has content -->
      class="p-4 font-medium text-xl border-b border-gray-200">
      <slot name="header"></slot>
    </h3>
    <div
      v-if="$slots.default" <!-- only render if default slot has content -->
      class="p-4 border-b border-gray-200">
      <!--       ^^^^^^^^^^^^^^^^^^^^^^^^ but even there is no footer, default slot will still be rendered with the border on the bottom, we want to remove this -->
      <slot></slot>
    </div>
    <div
      v-if="$slots.footer" <!-- only render if footer slot has content -->
      class="p-4">
      <slot name="footer"></slot>
    </div>
  </RoundedCard>
</template>

<script setup lang="ts">
import RoundedCard from '@/components/RoundedCard.vue';
</script>
```

As commented in the code, not only render slots conditionally, but we also need to remove the bottom border of the default slot if the footer slot is not passed any content. To achieve this, we can use conditional class technique with $slots to check if the footer slot has content, and then add the border class conditionally as below:

```vue
<template>
  <RoundedCard>
    <h3
      v-if="$slots.header"
      class="p-4 font-medium text-xl border-b border-gray-200">
      <slot name="header"></slot>
    </h3>
    <div
      v-if="$slots.default"
      :class="{
        'p-4': true, // padding is always there, so the value is always true
        'border-b border-gray-200': $slots.footer // only add border if footer slot has content
      }"
    >
      <slot></slot>
    </div>
    <div
      v-if="$slots.footer"
      class="p-4">
      <slot name="footer"></slot>
    </div>
  </RoundedCard>
</template>

<script setup lang="ts">
import RoundedCard from '@/components/RoundedCard.vue';
</script>
```

Of course, you can direclty use RoundedCard in BookingItem.vue instead of SectionedCard, but the point is that if we let the user always use the same component for the specific style subset, we can make the code cleaner and easier to read. And furthermore, we unify the styles of the component only with one component, which let other developers follow the design principle more easily.


