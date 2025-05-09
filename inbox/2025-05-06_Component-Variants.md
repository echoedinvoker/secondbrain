---
date: 2025-05-06
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Component Variants

```vue
<template>
  <button
    class="text-sm font-medium border border-gray-200 rounded-full px-3 py-1
    hover:bg-gray-100"
    >{{ label }}</button>
</template>

<script setup lang="ts">
defineProps({
  label: String,
})
</script>
```

The above is an example of a Button UI component. We want to modify it so that its style can change based on the values of the props passed in.

```vue
<template>
  <button
    <!--we can v-bind the class attribute with an array of classes-->
    :class="[
      'text-sm font-medium rounded-full px-3 py-1', <!-- common classes, always applied no matter its default or variant-->
      variants[variant] ?? variants.default, <!-- variant classes, use ?? to set a default value if the variant is not found -->
    ]"
    >{{ label }}</button>
</template>

<script setup lang="ts">

// define the variants object with the different styles
const variants = {
  default: 'border border-gray-200 hover:bg-gray-100',
  danger: 'bg-red-200 enabled:hover:bg-red-100 text-red-900'
}

defineProps({
  label: String,

  // define the variant prop with a default value
  variant: {
    type: String,
    default: 'default',
  }

})
</script>
```

Then, we can use the component like this:

```vue
<template>
  <SectionedCard>
  <div class="flex justify-between">
    <div>Booking to Vue Conference...</div>
    <Button label="Cancel" variant="danger" /> <!-- pass the variant prop to change the style -->
  </div>

  </SectionedCard>
</template>

<script setup lang="ts">
import SectionedCard from '@/components/SectionedCard.vue';
import Button from './Button.vue'; //  import the Button component
</script>
```

